---
layout: default
title: Day4
nav_order: 4

parent: Develop Project - W1
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 생활  코딩과 공부하기

[https://opentutorials.org/course/62/233](https://opentutorials.org/course/62/233) php

Php: Hypertext Preprocessor 

서버측에서 사용되는 언어로, 생각보다 우리가 접하는 많은 것들이 php로 이루어져 있다.  
php언어는 facebook, wikipedia 에도 사용되고 있으며, 웹에 국한된 언어임에도 불구,  
많은 사용률을 자랑하고 있으며 손쉬우면서도 개발에 용이하게 사용된다.

장점으로는 웹에 최적화되어 개발에 매우 효과적인 언어이다.   
많은 데이터베이스를 지원하며 이미 많은 소프트웨어들이 php로 이루어져 있기에  
접근성과 습득 시 효율성이 매우 높다.

단점으로는 웹 개발만을 위한 언어라는 점, 오픈 소스로 인한 낮은 예측 가능성이다.  
오픈소스로 인해 많은 사람들의 손을 타게 되면서 일관성이 떨어져 기억해야할 부분이 많다.



## PHP 시작하기

먼저 시작을 위해서는 `<?php` 로 열고, `?>` 를 이용하여 닫는다.  


<div class="code-example">hello world</div>
```php
<?php echo "hello world"; ?>
```

<br>

escape 문자 `\` 를 이용하여 `"`와 같이 약속되어 있는 기호를 나타낸다.
<div class="code-example">오늘은 "php"에 대해 공부했다</div>
```php
<?php echo "오늘은 \"php\"에 대해 공부했다"; ?>
```

<br>

문자를 나타내기 위해서는 `" "` 기호를 통해 묶어 주어야 하며 `.` 기호는 문자를 연결시킨다.
<div class="code-example">pineapplepen</div>

```php
<?php echo "pineapple"."pen"; ?>
```

<br>

변수를 남아내기 위해서는 `$` 기호를 사용하여 저장하며 형을 따로 지정하지 않는다.  
.php에서 개행을 쓰는 방법을 몰라 강의 그대로 따라했다.  
<div class="code-example">5<br>10 </div>
```php
<html>
<?php
	$a = 5;
	echo $a . '<br />';
	$a = $a + $a;
	echo $a;
?>
</html>
```

<br>

상수는 `define` 이라는 함수를 이용해 지정 가능하며 불변의 법칙이 적용된다.  
첫번째에는 상수의 이름, 두번째에는 상수의 값을 지정해주면 된다.
<div class="code-example">jamm</div>
```php
<?php
	define('my_name','jamm');
	echo my_name;
 ?>
```

<br>

아무 생각 없이 변수를 지정하여 사용하였는데 생각해보면 데이터 형식을 지정해야 하는게 아닌가 싶지만 php는 알아서 세팅하여 적용한다고 한다. `var_dump`를 이용하면 데이터 형식과 데이터 값을 출력할 수 있고, `gettype` 함수를 이용하면 데이터 형식만 받을 수 있다. `settype`을 이용하여 데이터 형식을 직접 지정할 수 있다.
<div class="code-example">integer<br>double </div>
```php
<html>
<?php
	$a = 777;
	echo gettype($a);
	settype($a, 'double');
	echo '<br />';
	echo gettype($a);
?>
</html>
```
신기하게도 형 지정을 잘못 지정하니 아예 결과물 출력이 되지 않았다.

<br>

가변변수라는 기능이 존재하여 한번 실습해보았다.  
변수의 이름을 동적으로 설정할 수 있는 PHP가 가진 유연함이라고 한다.
<div class="code-example">P4C</div>
```php
<html>
<?php
	$title = 'subject';
	$$title = 'P4C';
	echo $subject;
?>
</html>
```

<br>

비교 연산자는 잘 알려진 `>`,`==`, `<=`,`!=` 등이 존재했는데 그 중에서 특이한 비교 연산자가 존재했다.   
바로 `===` 인데 php에서 `==` 결과는 자동 형변환이 진행된 이후의 값이 반환되는데 비해 `===`는 데이터 형식까지 동일해야만 참을 반환한다.

<div class="code-example">1 == '1' : bool(true)<br>
1 === '1' : bool(false) </div>
```php
<html>
<?php
	echo "1 == '1' : ";
	var_dump(1 == '1');
	echo "<br />1 === '1' : ";
	var_dump(1 === '1');
?>
</html>

```



## HTML 그리고 PHP...

여태까지 내가 만든 페이지는 소스코드에 의한 출력 외에 아무것도 없는 "정적"인 기능만을 수행했다.  
이제부터는 HTML과 PHP를 이용하여 서버와 클라이언트가 데이터를 주고 받으며 기존과 다르게 수행되도록 할 것이다. 

첫 시작은 앞서 HTML에서 맛보았던 GET method를 이용한 소통이다.

```php
<?php echo $_GET['id']; ?>
```

위 코드를 작성한 php를 호출할 때 `~.php?id=jamm` 과 같이 작성하면 `$_GET['id']`에 값이 전달된다.  

![image-20220422153059806](../img/image-20220422153059806.png)

값의 이름과 값을 구분 지을 때에는 `=` 이 사용된다는 것을 알 수 있다.
또한 인자가 두 개 이상 전달할 때 구분 짓기 위해서는 `&`기호를 이용하여 구분한다.

```php
<?php echo $_GET['id'].$_GET['pw']; ?>
```

![image-20220422153427915](../img/image-20220422153427915.png)

그리고 눈치했듯이 앞서 배운 HTML과 결합한다면 사용자가 직접 주소창에 값을 전달하지 않더라도 `form` 태그의 `action` 속성과 `method` 속성을 이용하여 전달할 수 있음을 알 수 있다.

`POST` 방식을 이용해서 값을 전달하기 위해서는 `GET` 대신 `POST`를 사용하면 되는데,  
그 둘의 차이는 데이터 전달 방식의 차이가 존재한다는 점이다.  
`GET` 방식의 경우에는 URL에 전달되는 데이터가 보이지만, `POST` 방식의 경우는 그렇지 않다.  
따라서 외부적인 노출을 가려야하는 ID와 PW를 입력하는 폼에서는 `POST`를 채택하는 것이 일반적이다.

## HTTP 엿보기

login.html

```html
<!DOCTYPE html>
<html>
  <body>
    <form action="login.php" method="post">
      <p>아이디: <input type="text" name="id" value="Input your id"></p>
      <p>비밀번호: <input type="password" name="passwd" value="Input your password"> </p>
    </form>
  </body>
</html>
```



login.php

```php
<?php
echo $_POST["id"]. "," .$_POST["pwd"];
?>

```



<br>

위와 같이 코드를 작성한 뒤 `Burp suite`를 이용하여 login.html 에 접속했을 때,  
 서버와 클라이언트 간의 HTTP 통신을 확인해보면 다음과 같이 확인할 수 있다.

![image-20220422160626601](../img/image-20220422160626601.png)

클라이언트 측에서 request를 하게되면 서버측의 `apache`를 통해 전달된 response를 웹 클라이언트(웹브라우저)에서 html코드를 해석하여 사용자에게 보여주는 것이다.

POST방식을 사용하여 값을 전달했을 때 전달되는 정보도 한번 확인해보면 다음과 같다.

![image-20220422161341560](../img/image-20220422161341560.png)

`GET` 방식과 달리 URL에 표시되지는 않지만 HTTP 통신 내용에 입력한 `POST` 값이 전달되는 것을 볼 수 있다. php에서 값의 이름을 제대로 적지 않으면 안된다!


# > 한번 알아보았습니다.

- 웹 프로그래밍에서 php뿐만 아니라 python도 사용된다고 했다.  
  개발 시에 이 둘을 선택하는 기준이 있는 걸까?

>먼저 둘 다 오픈소스라는 배경을 지니고 있어 접근성이 용이하다.  
>하지만 PHP는 웹 개발 전용 연어인 반면 Python은 범용 풀 스택 프로그래밍 언어이다.  
>PHP는 서버 측 스크립팅 언어이며, Python은 객체 지향 스크립팅 언어이다.  
>아직까지는 많은 시스템에서 PHP를 채택되고 있지만 Python에 비해 낮은 보안성이 흠이다.   
>각 언어가 가진 장/단점이 존재하여 사용자가 원하는 방향성에 따라 달라진다고 생각된다.  
>나는 혼자 웹해킹 공부를 할 때 꽤 많은 문제들이 Python이였던 것 같다.
