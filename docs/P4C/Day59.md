---
layout: default
title: Day59
nav_order: 2

parent: Hacking Project - W9
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

내가 정시파이터였다는 사실을 잊었다. 감히 면접 준비를 이렇게 늦게 시작한게 정말 바보같다. 으앙

# > Webhacking.kr:old-026(php)


## 삽질

```php
<?php
  include "../../config.php";
  if($_GET['view_source']) view_source();
?><html>
<head>
<title>Challenge 26</title>
<style type="text/css">
body { background:black; color:white; font-size:10pt; }    
a { color:lightgreen; }
</style>
</head>
<body>
<?php
  if(preg_match("/admin/",$_GET['id'])) { echo"no!"; exit(); }
  $_GET['id'] = urldecode($_GET['id']);
  if($_GET['id'] == "admin"){
    solve(26);
  }
?>
<br><br>
<a href=?view_source=1>view-source</a>
</body>
</html>
```

id의 값이 "admin"이 되어야 하는데 preg_match에서 걸러지고 있다. 그리고 해당 필터링 아래에는 id 파라미터 값을 `urldecode` 함수를 통해 담아내고 있는데 아마 이부분을 활용해야 할 듯 하다.

<br>

## writeup

소스코드만 보고 가볍게 생각하면 urlencode를 해주면 되겠다는 생각을 했다.

```
id=%61%64%6d%69%6e
```

![image-20220615222845359](../img/image-20220615222845359.png)

그러나 실패로 떴고 디코딩된 문자로 변환되어 보였다.

<br>

다시 생각해서 전송 시점에 디코딩된다는 사실을 기억해내고, 두 번 인코딩해서 보내면 된다는 결론에 도달하였다면 이 문제를 풀 수 있었다. 인코딩은 인코딩 사이트를 이용하여 변환해버렸다.

```
%2561%2564%256d%2569%256e
```
![image-20220615223047791](../img/image-20220615223047791.png)

<br><br><br>

-----

# > Webhacking.kr:old-033(php)


## writeup

### Challenge 1
`https://webhacking.kr/challenge/bonus-6/index.txt`
```php
<hr>
Challenge 33-1<br>
<a href=index.txt>view-source</a>
<hr>
<?php
if($_GET['get']=="hehe") echo "<a href=???>Next</a>";
else echo("Wrong");
?>
```
"get" 이라는 파라미터에 GET방식으로 "hehe"를 전달하라고 한다.

```
get=hehe
```

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/lv2.php`이다.

<br>

### Challenge 2
`https://webhacking.kr/challenge/bonus-6/lv2.txt`
```php
<hr>
Challenge 33-2<br>
<a href=lv2.txt>view-source</a>
<hr>
<?php
if($_POST['post']=="hehe" && $_POST['post2']=="hehe2") echo "<a href=???>Next</a>";
else echo "Wrong";
?>
```
이번에는 "post" 와 "post2" 파라미터에 POST 방식으로 "hehe","hehe2"를 전달하라고 한다.

![image-20220615224040169](../img/image-20220615224040169.png)

burp suite 의 "Change request method" 기능을 통해 POST 방식으로 바꾸어 전달했다.

```http
POST /challenge/bonus-6/ HTTP/1.1
...
...
...
post=hehe&post2=hehe2
```

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/33.php`이다.

<br>

### Challenge 3
`https://webhacking.kr/challenge/bonus-6/33.txt`

```php
<hr>
Challenge 33-3<br>
<a href=33.txt>view-source</a>
<hr>
<?php
if($_GET['myip'] == $_SERVER['REMOTE_ADDR']) echo "<a href=???>Next</a>";
else echo "Wrong";
?>
```
"myip" 파라미터에 GET방식으로 현재 접속 IP정보를 대입하라고 한다. 

```
myip=[REMOTE_ADDR]
```
인터넷에 내 접속 아이피를 검색하여 대입했다.

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/l4.php`이다

<br>

### Challenge 4
`https://webhacking.kr/challenge/bonus-6/l4.txt`
```php
<hr>
Challenge 33-4<br>
<a href=l4.txt>view-source</a>
<hr>
<?php
if($_GET['password'] == md5(time())) echo "<a href=???>Next</a>";
else echo "hint : ".time();
?>
```
"password" 파라미터에 현재 시간을 md5 해시값을 넣어야한다. 다행이 time 정보는 출력되기에 조금 앞선 시간을 미리 계산하여 타이밍 맞게 넣어주면 되겠다.

```
password=36f6afac6fabeec908351f8f7716da2e
```
나는 `time()`의 값이 1655301340이 되는 시점에 위 해시값을 넣고 통과했다.  

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/md555.php` 이다.

<br>

### Challenge 5
`https://webhacking.kr/challenge/bonus-6/md555.txt`

```php
<hr>
Challenge 33-5<br>
<a href=md555.txt>view-source</a>
<hr>
<?php
if($_GET['imget'] && $_POST['impost'] && $_COOKIE['imcookie']) echo "<a href=???>Next</a>";
else echo "Wrong";
?>
```
GET, POST, COOKIE 값이 세팅되야 하는 문제이다. 메소드를 둘다 사용하는 경우를 생각해본 적이 없다. 하지만 기본에 충실하여 GET은 url로, POST는 패킷 body에, COOKIE는 개발자 도구로 설정해보자.

```http
POST /challenge/bonus-6/md555.php?imget=1 HTTP/1.1
Cookie: PHPSESSID=; imcookie=1
...
...
...
impost=1
```
다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/gpcc.php` 이다.

<br>

### Challenge 6
`https://webhacking.kr/challenge/bonus-6/gpcc.txt`
```php
<hr>
Challenge 33-6<br>
<a href=gpcc.txt>view-source</a>
<hr>
<?php
if($_COOKIE['test'] == md5($_SERVER['REMOTE_ADDR']) && $_POST['kk'] == md5($_SERVER['HTTP_USER_AGENT'])) echo "<a href=???>Next</a>";
else echo "hint : {$_SERVER['HTTP_USER_AGENT']}";
?>

```
"test" 쿠키 값을 접속 IP정보의 md5 해시 값으로, "kk" 파라미터를 POST 방식으로 접속 브라우저의 정보의 md 5해시값으로 전달해야 한다.

```http
POST /challenge/bonus-6/gpcc.php HTTP/1.1
Host: webhacking.kr
Cookie: PHPSESSID=; test=28fed3ef23de844dbbf61d38c7cad26f
...
...
...
kk=eab83d263c39dcece3e73f92ae2214fc
```
다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/wtff.php` 이다.

<br>

### Challenge 7
`https://webhacking.kr/challenge/bonus-6/wtff.txt`
```php
<hr>
Challenge 33-7<br>
<a href=wtff.txt>view-source</a>
<hr>
<?php
$_SERVER['REMOTE_ADDR'] = str_replace(".","",$_SERVER['REMOTE_ADDR']);
if($_GET[$_SERVER['REMOTE_ADDR']] == $_SERVER['REMOTE_ADDR']) echo "<a href=???>Next</a>";
else echo "Wrong<br>".$_GET[$_SERVER['REMOTE_ADDR']];
?>
```
접속 IP에서 `.` 기호를 없앤 뒤 이를 파라미터 명과 값으로 사용하면 된다.

```
127001=127001
```
`127.0.0.1`일 경우에 위와 같이 하면 된다.

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/ipt.php` 이다

<br>

### Challenge 8
`https://webhacking.kr/challenge/bonus-6/ipt.txt`
```php
<hr>
Challenge 33-8<br>
<a href=ipt.txt>view-source</a>
<hr>
<?php
extract($_GET);
if(!$_GET['addr']) $addr = $_SERVER['REMOTE_ADDR'];
if($addr == "127.0.0.1") echo "<a href=???>Next</a>";
else echo "Wrong";
?>
```
`extract()`를 통해 GET으로 받은 키-값쌍을 변수로 변환한다. `addr`이라는 변수에 `127.0.0.1`이 담기도록 파라미터를 지정해주면 되겠다.

```
addr=127.0.0.1
```

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/nextt.php` 이다.

<br>

### Challenge 9
`https://webhacking.kr/challenge/bonus-6/nextt.txt`
```php
<hr>
Challenge 33-9<br>
<a href=nextt.txt>view-source</a>
<hr>
<?php
for($i=97;$i<=122;$i=$i+2){
  $answer.=chr($i);
}
if($_GET['ans'] == $answer) echo "<a href=???.php>Next</a>";
else echo "Wrong";
?>
```
97부터 122까지 2씩 증가하는 정수를 문자로 변환하여 문자열을 만든 뒤 이를 `ans` 값으로 넣어주어야 한다. 97은 아스키코드로 알파벳 a이고 122번은 z이므로 두칸씩 띄어 문자열을 만들어보자

```
ans=acegikmoqsuwy
```

다음 공격 대상은 `https://webhacking.kr/challenge/bonus-6/forfor.php`이다.

<br>

### Challenge 10
`https://webhacking.kr/challenge/bonus-6/forfor.txt`

```php
<hr>
Challenge 33-10<br>
<a href=forfor.txt>view-source</a>
<hr>
<?php
$ip = $_SERVER['REMOTE_ADDR'];
for($i=0;$i<=strlen($ip);$i++) $ip=str_replace($i,ord($i),$ip);
$ip=str_replace(".","",$ip);
$ip=substr($ip,0,10);
$answer = $ip*2;
$answer = $ip/2;
$answer = str_replace(".","",$answer);
$f=fopen("answerip/{$answer}_{$ip}.php","w");
fwrite($f,"<?php include \"../../../config.php\"; solve(33); unlink(__FILE__); ?>");
fclose($f);
?>
```
연산식이 주어져있고, 해당 `./answerip/결과.php`로 이동하면 문제를 풀 수 있다고 한다. 똑같은 php를 만들어서 `echo $answer."_".$ip` 를 통해 어디로 가야하는지 찾아내면 되겠다.

```
answerip/XXXX_XXXX.php
```

와 드디어 풀었다.

![image-20220615233726668](../img/image-20220615233726668.png)



