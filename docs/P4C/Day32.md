---
layout: default
title: Day32
nav_order: 3

parent: Hacking Project - W5
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----
부끄럽게도 서울 자취방 입주 준비로 인해 어제 공부를 못했다.  
1 시간이라도 안 한 어제의 내 자신이 부끄럽다.

# > DVWA: Command Injection

![image-20220519100117332](../img/image-20220519100117332.png)

## 삽질



아래는 `view source`로 확인한 코드이다.

```php
<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Get input
    $target = $_REQUEST[ 'ip' ];

    // Determine OS and execute the ping command.
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
        // Windows
        $cmd = shell_exec( 'ping  ' . $target );
    }
    else {
        // *nix
        $cmd = shell_exec( 'ping  -c 4 ' . $target );
    }

    // Feedback for the end user
    echo "<pre>{$cmd}</pre>";
}

?> 
```
사용자의 ip주소를 입력받는 란이 명령어 문장에 그대로 포함되기에 명령어를 묶어서 사용할 수 있는 기호를 이용하면 유용할 것 같다. 현재 명령어를 연속적으로 전달하기 위해 알고있는 기호는 `&`, `||` 정도가 있어서 테스트 해보았다.

![image-20220519095150114](../img/image-20220519095150114.png)

위처럼 결과가 나왔는데 이 다음 내가 무엇을 해야할지 감이 오지 않아 구글링 해보았다.  
그런데 찾은 결과가 나와 동일해서 커맨드 인젝션에 사용할 수 있는 기호를 정리하기로 했다.

<br>

## Write up

Command Injection에서 다중 명령을 이용하기 위해서 사용하는 기호는 아래와 같다

`A && B` : A 결과가 성공한 경우 B를 실행

`A;B` : A 다음 B를 연속적으로 실행

`A || B` : A 결과가 실패할 경우 B 실행

`A | B` : A, B를 실행(파이프 라인)

정답

```bash
ping -c 4 8.8.8.8 && ls -al
ping -c 4 1 || ls -al
ping -c 4 8.8.8.8; ls -al
ping -c 4 8.8.8.8 | ls -al
```

<br><br><br>

------


# > DVWA: Cross Site Request Forgery (CSRF)

![image-20220519100737065](../img/image-20220519100737065.png)

사이트 간 요청위조. 이용자가 자신의 의지와는 무관하게 공격자가 의도한 행위를 특정 웹사이트에 요청하게 만드는 공격

## 삽질

```php
<?php

if( isset( $_GET[ 'Change' ] ) ) {
    // Get input
    $pass_new  = $_GET[ 'password_new' ];
    $pass_conf = $_GET[ 'password_conf' ];

    // Do the passwords match?
    if( $pass_new == $pass_conf ) {
        // They do!
        $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
        $pass_new = md5( $pass_new );

        // Update the database
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";
        $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

        // Feedback for the user
        echo "<pre>Password Changed.</pre>";
    }
    else {
        // Issue with passwords matching
        echo "<pre>Passwords did not match.</pre>";
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);
}

?>
```

위 코드를 읽지 않고 그냥 new password란과 confirm new password란을 채워 제출을 누르면 그냥 admin의 비밀번호가 변경되는 것을 확인했다. 이후 해당 문제의 목적이 무엇인지 이해하기 위해 관련한 정보를 수집해보았다.

CSRF를 이해하기 위해서는 먼저 SOP 보호 정책을 살펴보아야 한다.

브라우저는 타 사이트 접근 시 인증 정보인 cookie를 전달하는 특징이 있다. 이를 악용한다면 악의적인 페이지에서 정상적인 페이지로 cookie와 함께 요청을 보내고 응답 정보를 획득할 수도 있다. SOP는 scheme, host, port 모든 요소가 일치될 경우에만 정보 출처의 origin으로 인식하기에 위와 같은 상황을 예방한다. 그러나 <img>, <style>, <script> 등의 태그는 SOP의 영향을 받지 않는다.

CSRF는 공격자가 클라이언트를 대상으로 작성한 스크립트를 이용자의 환경에서 트리거 함으로써 임의 요청을 보내는 공격이다. img, form 태그와 같이 헤더에 이용자의 인증 정보가 포함되는 HTTP 요청을 활용하고, Javascript의 location.href, window.open()과 같은 함수를 활용하여 임의 페이지 접속을 유도한다. 결과적으로 해당 스크립트를 트리거 시킨 이용자의 권한으로 임의 기능을 실행할 수 있다. 2008년 1080만 명의 개인정보가 유출되었던 옥션 해킹사건에 사용된 공격기법이라고 한다. 비밀번호 변경에 자주 악용된다나 뭐라나!

<br>

결과적으로 다른 페이지에 접근한 권한으로 CSRF 페이지로 비밀번호 변경 요청을 보내도록 하는 것이 이번 문제의 목표라고 생각했다. 그리하여  GET방식으로 통신하는 점을 감안하여 `img` 태그에 CSRF 페이지 정보를 포함한 URL request를 전송하도록 XSS페이지에 스크립트를 올려야겠다.



<br>

## Write up

XSS 페이지로 접근한 권한으로 CSRF 페이지로 요청을 보내 "admin"의 비밀번호를 변경할 것이다.

![image-20220519111649353](../img/image-20220519111649353.png)

<br>

XSS 페이지에 접속한 다음에 다음 구문을 작성한다.

`<img src="http://192.168.3.128/DVWA/vulnerabilities/csrf/?password_new=1234&password_conf=1234&Change=Change">`

이 때 Message 박스에 입력 길이가 제한적으로 나타나는데 이는 개발자 도구를 이용하여 아래 부분의 `maxlength` 속성 값을 50보다 더 크게 설정해주면 된다.



![image-20220519111935765](../img/image-20220519111935765.png)



이후 XSS 페이지에 접속을 해보면 아래처럼 스크립트가 추가되는데

![image-20220519112837829](../img/image-20220519112837829.png)

burp suite를 이용하여 확인할 수도 있지만 개발자 도구에서도 아래와 같은 통신 기록이 보인다.

![image-20220519113017956](../img/image-20220519113017956.png)



제대로 적용이 되는지 확인하기 위해 CSRF 페이지에서 비밀번호를 "qwer"로 변경한 뒤에 XSS 페이지에 접속해보면 다시 비밀번호가 "1234"로 변경되어 있는 부분을 확인할 수 있다. 이렇게 앞으로 admin 접속 시에 1234로 로그인을 하게 되었다.

<br><br><br>

-----

# > DVWA: File Inclusion

![image-20220521102322270](../img/image-20220521102322270.png)

문제를 보자 마자 무엇을 해야할지 감이 온다.  
군대에서도 그렇고 일반 웹 탐색에서도 습관처럼 해보던 시도를 해봐야할 것 같다.

## Write up



![image-20220521102349171](../img/image-20220521102349171.png)

file3.php를 눌러봤는데 URL 부분을 보니 GET 방식으로 인자를 전달하여 작업 중인 디렉토리의 파일을 읽어오는 형식인 듯하다. file1,2,3 다음은 4가 아닐까라는 게싱을 해보았다.



![image-20220521102257998](../img/image-20220521102257998.png)

그리고 문제로부터 칭찬을 받았다 *^^*
