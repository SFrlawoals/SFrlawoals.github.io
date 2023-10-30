---
layout: default
title: Day13
nav_order: 6

parent: Develop Project - W2
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 어제의 삽질 

인증코드가 5분이 지나면 인증할 수 없도록 만드는 과정에서  

```php
<?php
$current_time =  date("Y-m-d H:i:s",time());
$createtime=$row['createtime'];
var_dump($current_time);
var_dump($createtime);
?>
```



![image-20220430143409892](../img/image-20220430143409892.png)

내 db 시간과 php 서버의 시간이 다르다는 것을 이제야 알았다 🤣🤣🤣🤣🤣🤣  
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ  
뭐든지 기초부터 점검해야 하는데 그새 잊어버렸다.

php 서버의 시간은 `php.ini ` 의 **date.timezone = Asia/Seoul** 를 수정하고,   
db 서버의 시간은 `my.cnf`에 **default-time-zone='+9:00'**를 추가했다.

궁극적으로 대문짝과 관련하여 오늘까지 완성한 사항은 다음과 같다.

1. 랜덤한 값 6자리를 이용한 이메일 인증
2. 이메일 미인증 시 가입 불가
3. 중복된 이메일,아이디,이름 가입 불가
4. 비밀번호 저장 시 암호화 및 로그인 시 복호화

전체적인 리뷰는 2주차 마지막에 할 예정이다. 

간단하게 정리하자면 이메일 인증은 php와 db의 합작으로 구현하였고, 데이터 전송과 관련하여 막혔던 부분이 있었는데 이는 COOKIE를 이용해보았다. 다른 코드를 이용하지 않다 보니 내가 내놓은  전략이였다.  다만 아직까지 이메일 전송에 시간이 오래 걸린다는 부분은 해결하지 못했다.

또한 이메일 인증 유효 시간을 5분으로 설정하고 싶었는데 이는 db와 연동되는 로직이 있었기에 컬럼을 추가하면서 기능을 넣어볼 수 있었다. 다만 그렇게까지 효율적이지는 못하다. 추후 db에 쌓인 데이터를 자동으로 지우는 기능이 없으면 db가 터지지 않을까 싶다.

중복된 정보는 쿼리문을 이용하여 가입된 정보가 있으면 불가능하도록 하였다. 하지만 로그인도 그렇고 가입도 그렇고 sql_injection에 취약한 것 같긴 하지만 지금은 기능 구현을 중점으로 하였다.

마지막으로 어제의 고민 사항이였던 것들에 대해 스스로 답해보자면

1. 일단 메일 발송이 너무 느리다. 30초 정도 걸린다.

   > 해결하지 못했다. 찾아보니 php 메일 함수로는 힘들다고 하는데...

2. 인증 이후에 입력 값을 바꾸면 어떡하지?

   > 재밌게도 php에서 javascript의 document. 함수를 사용했는데  
   > history.go(-1) 를 사용해서 인지 반영이 되질 않는다.  
   > 이메일 인증 이후에 해당 폼을 disable 처리를 하고 싶긴 하다.

1. 인증 시간에 제한은 어떻게 만들 것인지?

   > db 연동과 함께 생성 시간을 컬럼을 만들면서 로직을 만들었다.

2. 난수를 때려 맞추면 어떡하지?

   > rand함수의 조합으로 이루어졌지만 언젠가 /dev/urandom을 이용할 수 있다면...



다음 단기 목표는 **댓글, 추천, 파일 업로드 + 가능하다면 무한f5 방지용 1시간 마다 갱신 가능하게**

<br><br><br>

-----

# > 성장 과정

기본적인 틀을 인터넷에서 참고한 뒤 전부 직접 써 내려가다 보니 이것저것 많은 정보들을 열람해보게 되었다. 알게 된 소소한 정보들을 잠시 정리하고 넘어가야겠다.

## php의 sendmail

이 아이를 사용하기 위해서 참 많은 시간을 허비했다.  
가장 먼저 php 코드에서 `mail()`을 사용하고 싶다면 점검해야 할 사항은 

```bash
$ telnet localhost 25
```

를 이용하여 sendmail 자체가 운영체제에 있는지 알아야 한다.  
(참고로 telnet 들어갔다가 나오려면 ctrl+']' 키를 눌러야 탈출 된다!!!!)

만약에 `telnet: Unable to connect to remote host: Connection refused` 라는 문구가 뜬다면 sendmail이 없으므로 설치를 해야 한다.

```bash
$ sudo apt update
$ sudo apt-get install openssl sendmail sasl2-bin
$ sudo sendmailconfig
```
설정 사항들은 'Yes'로 응답

<br>

```bash
sudo vi /etc/mail/sendmail.mc
```
해당 설정 창에서 아래 정보를 수정했다.
```
DAEMON_OPTIONS(`Family=inet,  Name=MTA-v4, Port=smtp, Addr=127.0.0.1')dnl
DAEMON_OPTIONS(`Family=inet,  Name=MSP-v4, Port=submission, M=Ea, Addr=127.0.0.1')dnl
=>
dnl DAEMON_OPTIONS(`Family=inet,  Name=MTA-v4, Port=smtp, Addr=127.0.0.1')dnl
dnl DAEMON_OPTIONS(`Family=inet,  Name=MSP-v4, Port=submission, M=Ea, Addr=127.0.0.1')dnl
```
어떤 의미인지는 아직 모르지만 다른 정보에 의하면 Addr을 0.0.0.0으로 설정했다.

<br>
설정 반영 이후에 호스트를 설정한다.
```bash
m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf
vi local-host-names 
```
자신의 도메인명을 작성한다. 아마도 Jminis.github.io 처럼..?

<br>

서버 호스트를 설정하는 곳으로 속도가 미세하게 빨라진다고는 한다.
```bash
vi /etc/hosts
```
127.0.0.1       localhost localhost.localdomain 내 도메인
{: .code-example}

<br>

메일에 접근 가능한 정보를 설정하는 곳이다.
```bash
vi /etc/mail/access
```
<div class="code-example">
Connect:198.143.155     REJECT<br>
Connect:193             REJECT<br>
Connect:도메인    REPLY<br>
Connect:내 아이피 REPLY
</div>

<br>

가장 마지막에는 재시작을 통해 적용을 시키면 이후에 발송이 가능해진다
```bash
service sendmail restart
```

<br>

그리고 이 이후에 가장 중요한 작업이 있었는데, 나는 bitnami를 이용하여 웹 서버를 운영하려고 하다보니 sendmail과 관련된 설정을 해주어야 하는 점을 모르고 있었다.  
궁극적으로 sendmail을 설치하고 설정을 해주어도 bitnami에 있는 php의 설정을 수정해주지 않으면 절대 php의 `mail()` 사용이 불가능하다.  
그래서 설정해주어야 하는 파일은 `/opt/lampstack-8.1.4-0/php/etc/php.ini` 이다.  
```sh
; For Unix only.  You may supply arguments as well (default: "sendmail -t -i").
; https://php.net/sendmail-path
;sendmail_path = "env -i /usr/sbin/sendmail -t -i"
```
처음에 가면 이러한 상태로 되어 있는데 `sendmail_path` 앞에 있는 주석 `;`를 제거해야 한다. 또한 외국 사이트에서 아래처럼 무언가를 추가하면 된다고 하여 추가했었다.

```sh
sendmail_path = "env -i /usr/sbin/sendmail -t -i -O DeliveryMode=b"
```
이 모든 과정이 끝나서야 어제 잘 수 있었다!

<br><br>

## HTML 반영
웹 사이트를 만들다가 마음에 안드는 css나 전체적인 틀을 잡는 작업을 할 때 있었던 일이었는데, 도저히 선택자의 우선순위가 적용이 되질 않았다. 예로 태그 선택자를 이용하여 꾸민 css는 아이디 선택자를 이용한 css 보다 낮은 우선 순위여야 하는데 반영이 되질 않는 것이다. 생각보다 레이아웃을 수정하는데 많은 시간을 잡아먹다가 찾아보니 쿠키값에 의해 이전의 css정보를 브라우저에서 저장을 하고 있다고 한다. 
결론적으로 쿠키 값을 삭제하고 나니 적용이 안되던 우선 순위라던지 기타 설정들이 한번에 적용이 되었다. 정말 사소한 정보이지만 이러한 부분조차 무지하여 시간이 많이 잡아먹혔다.

수정한 CSS가 적용이 안된다 싶을 땐 쿠키값을 삭제해보자
{: .text-red-000}

<br><br>

## Mysql
db관련해서 할 말이 많은데 일단 시,분,초 단위까지 날짜를 출력하려면 type을 DATETIME로 해야한다. 코드 리뷰 시간에 많이 풀어둬야 겠다. 그리고 생각보다 php 파일을 엄청 많이 만들고 db를 연동할 일이 많아서 include 를 이용하여 외부 함수를 쓰는 방법이 유용했다. 오늘 너무 복잡하고 반복적인 코드들은 함수로 정리해버렸다.

오늘은 댓글 구현할 때까지 안자야징

<br><br><br>

------

# > 댓글 구현

![image-20220430210508510](../img/image-20220430210508510.png)

삭제 기능을 포함하여 구현을 했는데 생각해보니 게시물도 그렇고 여러 개일 경우에 다음 페이지로 넘어갈 수 있는 방법을 고려하지 않았다. 추천이랑 파일 업로드 이후에 게시판 검색 기능을 추가할 때 고려해보아야 겠다.
