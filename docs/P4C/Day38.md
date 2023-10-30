---
layout: default
title: Day38
nav_order: 3

parent: Hacking Project - W6
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----
중간 난이도로 설정한 뒤 시작!

# > DVWA: Insecure recaptcha

## 삽질

```php
...
	// Show next stage for the user
	echo "
	<pre><br />You passed the CAPTCHA! Click the button to confirm your changes.<br /></pre>
	<form action=\"#\" method=\"POST\">
	<input type=\"hidden\" name=\"step\" value=\"2\" />
	<input type=\"hidden\" name=\"password_new\" value=\"{$pass_new}\" />
	<input type=\"hidden\" name=\"password_conf\" value=\"{$pass_conf}\" />
	<input type=\"hidden\" name=\"passed_captcha\" value=\"true\" />
	<input type=\"submit\" name=\"Change\" value=\"Change\" />
	</form>";
} 
...
// Check to see if they did stage 1
    if( !$_POST[ 'passed_captcha' ] ) {
        $html     .= "<pre><br />You have not passed the CAPTCHA.</pre>";
        $hide_form = false;
        return;
    }

```
step2로 넘어가기 전에 `passed_captcha`라는 값이 true인지 확인하는 루틴이 추가되었다. burp suite로 POST값도 수정가능하기에 1차 2차를 정상적으로 진행해 본 뒤 그 패킷을 그대로 또 넘겨주면 될 것 같다.

<br>

## Write up

정상적인 진행 방식일 때
{: .text-blue-000}

step1의 패킷 정보이다.

![image-20220525233103726](../img/image-20220525233103726.png)

step2의 패킷 정보이다.

![image-20220525233148716](../img/image-20220525233148716.png)

<br>

비정상적인 요청
{: .text-red-000}

![image-20220525233409404](../img/image-20220525233409404.png)

burp suite 의 repeater 기능을 이용해 step2의 패킷만 전송했다. 바로 비밀번호가 변경된 모습이다.



<br><br>

오늘 드디어 입주 6일만에 집정리가 90%되었다. 드디어 와이파이가 생겨서 집에서 할 수 있게 되었고 컴퓨터도 설치했다. 생애 첫 내 컴퓨터이다. 하루 종일 택배만 뜯고 세팅했는데 밤이 되어서야 끝났다. 그래도 sqld 기출문제와 DVWA 한 문제라도 만져보고 자는 것에 뿌듯함을 느낀다. 내일은 진짜 하루 종일 공부만 할 수 있어서 행복하다.
