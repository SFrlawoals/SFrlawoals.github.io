---
layout: default
title: Day5
nav_order: 5

parent: Develop Project - W1
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 생활 코딩과 공부하기

[https://opentutorials.org/course/62/5122](https://opentutorials.org/course/62/5122) php

## 조건문
C언어에서 배울 수 있는 조건문과 별 차이가 없다.

```php
<?php
if (true){ echo 1; }

if (1){ echo 1; }

if (true and 1){ echo 1; }

if (0 or true){echo 1;}

if (!false){ echo 1; }

if (false){}
else if(true){ echo 1;}

if (1 == '1'){ echo 1; }

if (1 === '1'){}
else{echo 1;}

?>
```

위 코드의 총 8개의 조건문 중에서 8개 모두 1을 출력하는 결과가 나온다.  
`true` 와 `1` 은 둘 다 참의 의미를 가지며 그 반대는 `false`이다.  
또한 `else if ` 와 `else`가 존재하여 조건 분기를 정할 수 있다. 

조건 설정 시 `and`를 이용하여 "그리고" 의 의미를, `or`을 이용하여 "또는"의 의미를 표현한다.  
`!` 기호를 이용하여 부정의 의미를 더할 수 있다.

이전에 비교 연산자에서 `==`과 `===`의 차이를 맛만 보았었는데, 실제로 적용이 가능했다.  
정수와 문자열이 비교되는 경우 문자열이 숫자로 변환되어 비교되기 때문에,  
`==` 기호를 사용할 경우에 참이 되지만 `===` 기호의 경우는 거짓이 된다. 

<br><br>



## 반복문

반복문도 C언어에서 접한 것과 같은 유형으로 사용된다.  
이래서 컴퓨터를 시작하면 C언어부터 시작하라고 하는 것 같다.

```php
<?php

$i = 1;
while($i <= 10){
    echo "i= {$i}<br />";
    $i++;
}

for($i = 0; $i < 10; $i++){
    echo 'i*2= '.($i*2)."<br />";
}

$i = 1;
while(true){
	echo "i= {$i}<br />";
    if($i == 10) break;
    else{
        $i++;
        continue;
    }
}

for($i = 1;$i <= 2;$i++){
	for($j=1;$j <= 5;$j++)
        echo 'i*j= '.$i*$j,"<br />";
}

?>
```

위 코드는 모든 반복문에서 10줄의 결과물이 출력된다.
for 문과 while문은 거의 사용법이 유사하였지만 변수 앞에 항상 `$` 기호를 붙여야 한다.  
당연히 중첩 반복문은 사용가능하고 break와 continue가 존재한다.

조금 특이한 점이 있다면 문장 내에서 변수를 출력할 때 python처럼 사용가능하다는 점이다.  
`{ }` 기호를 이용하여 변수를 묶어 나타낼 수 있고, `.`을 이용하여 사이에 넣을 수도 있다.   
그리고 for문 자체의 증감식에서 `;` 기호는 붙이지 않는다.

<br><br>



## 함수

코드의 재사용성을 높여주는 "함수"를 잘 이용하여 깔끔하게 코딩하도록 하자.

```php
<?php
function printing(){
	echo "100";
}

function add_arg($i,$j){
	return $i+$j;
	return 200;
}

function default_arg($i=100){
	return $i;
}

printing();
echo add_arg(30,70);
echo default_arg();
echo default_arg(1);

?>
```

위 함수 출력 결과 4개 중 가장 마지막 1출력 이외에 모두 100이 출력된다.  
다른 언어의 함수 사용과 별반 다를 것은 없지만, 반환형을 지정하지 않아도 된다.  
또한 세번째 함수와 같이 `=`을 이용하여 인자의 기본값을 설정하면 따로 인자가 전달되지 않을 때,  
설정된 기본값으로 값이 설정되기 때문에 인자가 전달이 되지 않아도 100이 출력된다.

<br><br>



## 배열

C언어에서의 배열, Python에서의 리스트에 해당하는 영역이다.  
<div class="code-example">One<br>
Two<br>
Three<br>
Four<br>
Five<br>
Six<br>
1<br>
2<br>
3</div>
```php
<?php

function print_array($_arr){
    for($i = 0; $i < count($_arr); $i++)
        echo ucfirst($_arr[$i]).'<br />';
}

$e_array = ['one', 'two', 'three'];
$n_array = array(1,2,3);

array_push($e_array,'four');
$e_array = array_merge($e_array,['six','seven']);
array_unshift($e_array,'zero');
array_splice($e_array, 5, 0, 'five');
array_shift($e_array);
array_pop($e_array);
rsort($n_array);
sort($n_array);

print_array($e_array);
print_array($n_array);

?>
```
배열은 함수의 인자로도 반환값으로 사용이 가능하다.  
선언 방식은 php 버전이 5.4 이하일 경우 `array()`를 사용하여야 한다.   

배열의 끝에 값을 추가할 때에는 `array_push()`를 사용하고,  
배열의 끝에 값을 여러개 추가할 때에는 `array_merge()` 를 사용한다.  
merge 함수 사용 시에 결과값을 받을 변수가 존재해야 한다.

`array_shift()` 를 사용하여 배열의 처음에 값을 추가하고,  
`array_splice()` 를 사용하여 배열의 특정 인덱스에 값을 추가한다.  
`array_shift()`는 배열 제일 앞의 값을 제거하며,  
`array_pop()`은 배열의 제일 끝의 값을 제거한다.  
배열의 앞에 값을 조정할 때에는 shift, unshift/뒤에 값을 조정할 때에는 push,pop이다.

정렬 시에는 `sort` 함수와 `unsort` 함수를 이용하여 편하게 정렬가능하다.  
추가로 print_array 함수에서 사용된 `ucfirst()`는 문자열의 앞 문자를 대문자로 만든다.

Python에 dictionary와 같은 느낌의 데이터형이 있는데 바로 "연관배열" 이다.  
또한 인덱스에 숫자 또는 문자가 들어와도 구분짓지 않는 특성 때문에 php에서는 indexed array와 associative array를 결합하여 사용되어지고 있다.

<div class='code-example'>array(3) { ["one"]=> int(1) [2]=> string(3) "two" ["three"]=> int(3) }<br>key: one value:1<br>key: 2 value:two<br>key: three value:3</div>
```php
<?php
$number = array('one'=>1, 2=>'two', 'three'=>3);
//or $number = []; $number['one'] = 1; ... 생성 가능
var_dump($number);
echo "<br />";
foreach($number as $key => $value){
    echo "key: {$key} value:{$value}<br />";
}
?>
```

`foreach`를 이용하여 배열의 담긴 요소의 숫자만큼 반복문을 실행할 수 있다.  



<br><br><br>

-----

# > 한번 알아보았습니다.

- 과제와는 상관이 없지만 php 실습을 하던 도중 코드 수정 반영이 빨리되지 않았다.  
  그 이유가 존재하는걸까?

> 먼저 해결을 위해서는 `php.ini`의 opcache.enable=1 => **opcache.enable=0** 으로 수정하니 반영이 빠르게 되었다. 그런데 왜 이런 설정이 되어있는지 궁금했다.  
> 설정값에서도 볼 수 있듯이 `opcache` 라는 단어를 확인할 수 있는데, 해당 설정은 매번 php를 로드할 때마다 새로 컴파일을 진행하는 특성을 보완하기 위한 수단이다. 기존에 로드했던 php를 메모리에 넣어 두었다가 재사용하는 방식으로 재컴파일 시간을 단축시킨다.  
> 하지만 이로 인해 새로고침 시에 새로 컴파일된 정보를 빠르게 읽어오지 않아서 해당 설정값을 꺼버림으로서 빠른 반영이 되도록 설정하였다.  
> 해당 설정값을 적용하기 위해서는 apache를 재시작해야 한다.
>
> 









