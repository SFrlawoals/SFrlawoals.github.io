---
layout: default
title: Day30
nav_order: 2

parent: Hacking Project - W5
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > DVWA: Brute Force

## 삽질

![image-20220517173235746](../img/image-20220517173235746.png)

문제를 처음 마주했는데 어떤 계정으로 로그인을 해야하는지 몰랐기에 아래 `view source`를 눌렀다.

```php
<?php

if( isset( $_GET[ 'Login' ] ) ) {
    // Get username
    $user = $_GET[ 'username' ];

    // Get password
    $pass = $_GET[ 'password' ];
    $pass = md5( $pass );

    // Check the database
    $query  = "SELECT * FROM `users` WHERE user = '$user' AND password = '$pass';";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    if( $result && mysqli_num_rows( $result ) == 1 ) {
        // Get users details
        $row    = mysqli_fetch_assoc( $result );
        $avatar = $row["avatar"];

        // Login successful
        echo "<p>Welcome to the password protected area {$user}</p>";
        echo "<img src=\"{$avatar}\" />";
    }
    else {
        // Login failed
        echo "<pre><br />Username and/or password incorrect.</pre>";
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);
}

?> 
```

그런데 읽어보니 특정 계정으로의 로그인이 아니다. 그럼 일단 아이디부터 찾아야하는 것인가?

일단 로그인 쿼리문에서 sql injection이 가능하다고 생각되었고, 로그인의 성공 조건이 쿼리 응답이 존재하면서 그 결과가 하나이면 되기에 무작정 admin이라고 아이디를 생각하고 넣어봤다. 그랬더니

![image-20220517175256416](../img/image-20220517175256416.png)

띠용하게도 로그인이 됬다...? 

<br>

근데 인텐디드 풀이가 아니라고 생각되어 나와있는 라업을 보았다. 당연하게도 무차별 대입 공격으로 푸는 문제가 맞았고 이를 위한 여러 도구를 확인해볼 수 있었다. 다만 특별한 점이 있었다면 모든 경우의 수를 대입하는 것이 아닌 자주 사용되는 단어들을 중점으로 대입하는 방식이였다. 그 중에서 다루어 보았던 burp suite와 처음 보는 hydra를 이용해 직접 해보기로 했다.



## Write up 1 (with burp-suite)

burp suite의 ''Intruder" 기능을 사용하기 위해서 메뉴를 선택하면 아래와 같은 화면을 볼 수 있다.

![image-20220517181722219](../img/image-20220517181722219.png)

공격 전에 앞서 해당 사이트에서 통신한 HTTP 통신 기록이 있어야 한다.

![image-20220517182002353](../img/image-20220517182002353.png)

이후 request 패킷에서 우클릭 후 'Send to Intruder' 를 이용해 전송할 페이로드를 설정할 수 있다. 이 때 설정을 통해 모든 문자를 조합하여 대입하는 `brute forcer`와 특정 단어 리스트를 이용하여 해당 목록들만 대입하는 `sample list` 방식이 있다.

![image-20220517183804869](/img/image-20220517183804869.png)

다만 현재 패스워드의 길이도 모르기에 대입 경우의 수가 상대적으로 적은 사전(Dictionary) 대입를 우선 시도해보는 것이 맞겠다. 그리하여 인터넷에서 `wordlist_top_500PswMangled.txt`를 얻었고, 이를 대입해보기로 했다.

<br><br>

프로 버전이 아니여서 인지 너무나도 느렸고, 찾아줄 때까지 저녁을 먹고 왔으나 찾지 못했다. 결국 찾긴 했으나 그 사이에 시도한 히드라가 훨씬 더 빨랐다. 그래도 이런 기능이 있다는 것을 배울 수 있었다.

<br><br><br>

## Write up 2 (with hydra)

칼리 리눅스의 기본 프로그램으로 패스워드 크랙 시에 사용한다. 우분투 환경에서 사용하기 위해서는 따로 설치해야 하는데 설치 경로와 설치 명령어는 아래와 같다. (우분투 20.04 LTS에서 진행했다.)

```sh
git clone https://github.com/vanhauser-thc/thc-hydra
./configure
make
sudo make install
sudo apt-get install libssl-dev libssh-dev libidn11-dev libpcre3-dev \
libgtk2.0-dev libmysqlclient-dev libpq-dev libsvn-dev \
firebird-dev libmemcached-dev libgpg-error-dev \
libgcrypt11-dev libgcrypt20-dev
```

이후 `hydra`라는 명령어를 입력하면 사용이 가능하다. 기본적인 문법이 아래와 같이 나와있다

```sh
Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]
Example:  hydra -l user -P passlist.txt ftp://192.168.0.1
```

나는 브루트 포싱하여 로그인을 하는 것이 목표이기 때문에 예시를 참고하여 아래와 같은 명령어를 완성했다.

```
hydra -l admin -P ./wordlist_top_500PswMangled.txt 192.168.3.128 http-get-form "/DVWA/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login#:Username and/or password incorrect."
```

근데 재밌게도 유요한 비밀번호가 16개가 나왔다고 하는데 사실 직접 입력해보면 로그인이 안된다. 이거 뭐냐?



