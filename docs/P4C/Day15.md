---
layout: default
title: Day15
nav_order: 1

parent: Develop Project - W3
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 게시판 및 게시글

검색 이전에 떠오른 것은 페이징이였는데, 우연히 쉬면서 웹툰을 보다가 떠올랐다.  
게시글이 많으면 무한히 증가하는 css 때문이 고민했던 흔적이 있었기에 바로 진행했다.

```php
$list = 5;
$block_cnt = 5;
$block_num = ceil($page / $block_cnt);
$block_start = (($block_num - 1) * $block_cnt) + 1;
$block_end = $block_start + $block_cnt - 1;

$total_page = ceil($total / $list);
if($block_end > $total_page){
	$block_end = $total_page;
}
$total_block = ceil($total_page / $block_cnt);
$page_start = ($page - 1) * $list
```
구글링에서 찾을 수 있는 코드였는데 내가 의도하던 페이징 처리와 너무 동일하여 그대로 인용했다. 변수의 이름마저 거를 타선이 없어서 코드 이해 후 적용했더니 잘 되었다.
###### [https://transferhwang.tistory.com/64](https://transferhwang.tistory.com/64) 출처이다.

생략된 코드 부분까지 합쳐 요약하자면 게시물 총 갯수를 쿼리를 날려 얻어낸다. 이후 화면에 보여줄 게시글 갯수와 화면에 보여줄 페이지 갯수를 정함으로써 출력하는 시점에 적용한다. 또한 현재 페이지 값까지 기록하면서 보이는 게시물들이 밀려서 보이지 않도록 한다.

<br>

아래는 `search.php`의 일부분인데 검색을 위한 페이지를 따로 만드는 것보다는 기본적인 메인 페이지에서 검색 결과를 보여주려고 하였다. 때문에  쿼리 부분을 제외한 코드는 `main.php`와 동일했다.
```php
$sql = "SELECT * FROM story".(isset($_GET['track'])? " WHERE track='".$_GET['track']."'" :"" );
$key = $_GET['key'];
$value = $_GET['value'];
if($key == 'writer'){
	$ssql = "SELECT * FROM ($sql) AS sub WHERE $key=$value";
}else{
	$ssql = "SELECT * FROM ($sql) AS sub WHERE $key  like '%{$value}%'";
}
```
`LIKE` 문을 이용하여 제목이나 내용, 작성자를 검색하여 결과를 받을 수 있다.  
`search.php`를 짜는 과정에서 생각보다 기초적인 실수를 자주 했는데,  
다른 php로 넘어올 때 데이터를 넘기기 위해서는 `GET` 메소드를 잘 활용해야 했다.  
`form` 태그를 사용하지 않고 `a` 태그나 `button` 태그를 이용하려니 좀 힘들었다.

<br>


```php
setcookie("readNo_".$idx, $idx, time() + 60 * 60);
```
조회수를 1시간 마다 갱신 가능하도록 COOKIE 값을 이용했다.  
쿠키 값은 사용자가 변조 가능하고 쿠키 값을 삭제할 경우에 적용이 힘들지만,  
DB를 이용하여 적용을 했을 때 너무 많은 데이터를 누적해야할 것 같아 전자를 택했다. 

<br>

게시판을 선택했을 때 해당 게시판이 선택된 것을 인지할 수 있도록 조치했다.

![image-20220502153701025](../img/image-20220502153701025.png)

좀 코드가 더럽다 ㅎㅎ

<br><br><br>

-----

# > 회귀
 부산을 떠나 서울 자취방을 구해야 하는 관계로 하루를 어떻게든 비워야만 했다. 그리하여 하루도 쉬지 않고 과제를 진행해왔고 개발 목표를 향해서만 몰두했다. 

 그리고 2주차가 지나 글들을 돌아보니 어느 정도의 개념만 잡힌 상태에서 무작정 만들어 내겠다는 의지로만 보내온 것 같다. 물론 과정에서 많은 시행착오를 겪고 학습을 했지만 차분하게 정의 및 응용법들을 정리하지 못한 것 같다. 그리하여 서버 호스팅을 시작하기 전에 한 번 코드를 정리하며 사용했던 개념들을 기록(3주차 과제도 할겸)할 예정이다. 또한 정리 이후에는 개념들을 한 곳에 모아두는 글도 작성해야 겠다.

 끝난 건 아니지만 로컬에서 만들어진 페이지를 보니 뿌듯하다.


<br><br><br>

-----
# > PHP 정리

## db_conn.php
```php
<?php
// 디버깅용
error_reporting( E_ALL );
ini_set( "display_errors", 1 );

function db_connect(){
        $conn = mysqli_connect("localhost","root","","my_web") or die ("Can't access DB");
        return $conn;
}

function mq($sql){
    $conn = db_connect();
    $result = mysqli_query($conn, $sql);
    mysqli_close($conn);
    return $result;
}

function mqr($sql){
    $conn = db_connect();
    $result = mysqli_query($conn, $sql);
    $row = mysqli_fetch_assoc($result);
    mysqli_close($conn);
    return $row;
}

function alert_msg($msg){
    echo "<script>alert('$msg');</script>";
}
function history_go(){
    echo "<script>history.go(-1);</script>";
}
function location_replace($url){
    echo "<script>location.replace('$url');</script>";
}

?>
```
 어떠한 언어를 사용하든 반복적으로 자주 사용하는 함수는 정의하여 사용하는 것이 간결하고 효율적이다. 때문에 db접속부터 쿼리 전송, 자바스크립트를 이용한 알림창과 같은 기능들을 함수로 만들어 두었다. 다른 php에서 사용할 경우에 `include()`를 이용하여 외부 php의 함수를 참조할 수 있다.  

**error_reporting( E_ALL );**  
**ini_set( "display_errors", 1 );**  
이 두 줄의 코드 덕분에 오류를 빨리 찾을 수 있었다. php 코드는 `;` 기호 하나만 빠져도 실행이 되지 않았고, 틀린 값이 있어도 `php.ini`의 설정 때문에 출력을 해주지 않아 디버깅이 힘들었다. 하지만 저 친구가 있어줘서 빠르게 고칠 수 있었다.

 `mysql_connect()`와 `mysql_close()`는 mysql로 접속 연결/해제를 담당하는 함수이다. 첫번째 인자인 `localhost`는 외부 서버에서 접속할 경우 접속 서버 주소를 대신 전달해야한다. 두번째 인자인 `root`는 접속 계정이고 세번째 인자는 계정의 비밀번호 란이다. 네번째 인자는 접속할 db의 데이터베이스이다. 고로 위 코드에서 나의 데이터베이스 이름은 `my_web`인 것이다. `mysql_query()`는 연결된 db의 핸들링과 쿼리문을 전달하면 해당 db로 질의 후 응답을 반환한다. 그리고 `mysqli_fetch_assoc()`을 통해 전달받은 응답의 자료형을 배열로 변환한다. 

 `location.replace()`는 인자로 전달된 페이지로 이동하는 함수이다.  
`alert()`는 인자로 전달된 문자열이 알림창으로 뜨는 함수이다.

 `db_connet`와 같은 민감한 정보는 git 레포와 같은 곳에 올리지 않도록 주의하자.

<br><br>

## login.php

```php
<?php
include ('db_conn.php');

session_start();
$uid = $_POST['userid'];
$upw = $_POST['userpw'];

$sql = "SELECT * FROM userinfo WHERE userid='$uid' AND userpw=sha2('$upw',256)";
$result = mq($sql);
$row = $result->fetch_array();

if($result->num_rows == 1){
    $_SESSION['userid'] = $row['userid'];
    $_SESSION['usertrack'] = $row['usertrack'];
    $_SESSION['username'] = $row['username'];
    location_replace('main.php');
    exit;
}else {
    alert_msg('로그인 정보가 올바르지 않습니다.');
    history_go();
   exit;
}
?>
```
 `$_POST`는 HTML의 `form` 태그로부터 전달된 `post` 메소드 정보를 담고 있다. 

 `session_start()`는 세션을 사용할 때 사용하여 세션 정보를 기록할 수 있다. 세션 정보를 기록함으로써 메인 페이지에는 미로그인 사용자의 접근을 막을 수 있다.

 사용자의 정보가 데이터베이스에 존재하는지 확인하기 위하여 데이터를 조회하는 `SELECT` 구문을 이용했다. `WHERE`절을 이용하여 특정 정보만을 결과로 받을 수 있고, 비밀번호 조회 시 Mysql 내장 함수인 `SHA2()`를 사용한 이유는 회원가입 시 SHA-256로 암호화 했기 때문이다. 단방향 암호이기 때문에 입력한 값을 똑같이 암호화하여 저장된 비밀번호와의 일치여부로 참을 판별한다.

 로그인을 마치고서야 비로소 메인 페이지에 접근할 수 있다.

<br><br>

## signup.php
```php
<?php
include ('db_conn.php');
$uemail = $_COOKIE['useremail'];
$uid = $_POST['userid'];
$upw = $_POST['userpw'];
$upwc = $_POST['userpw_ck'];
$uname = $_POST['username'];
$utrack =  $_POST['usertrack'];

if($uemail == NULL){
    alert_msg('이메일 인증을 진행해주세요.');
    history_go();
}
if($uid == NULL || $upw == NULL || $upwc==NULL || $uname==NULL || $utrack==NULL){
    alert_msg('모든 정보를 입력해주세요');
    history_go();
    exit;
}else if($upw != $upwc){
    alert_msg('비밀번호와 비밀번호 확인이 일치하지 않습니다.');
    history_go();
    exit;
}else{
    $check_sql="SELECT userid from userinfo WHERE userid='$uid' or useremail='$uemail'";
    $result=mq($check_sql);
    if($result->num_rows >= 1){
        alert_msg('아이디 또는 이메일 정보가 이미 사용 중입니다.');
        history_go();
        exit;
    }

    $cksql = "SELECT checked FROM varify WHERE useremail='$uemail' ORDER BY createtime DESC LIMIT 1";
    $row=mqr($cksql);
    if($row['checked'] == FALSE){
        alert_msg('인증되지 않은 이메일 정보입니다.');
        history_go();
        exit;
    }

    $sql = "INSERT INTO userinfo (useremail,userid,userpw,username,usertrack,createtime) 
    VALUES('$uemail','$uid',sha2('$upw',256),'$uname','$utrack',now())";
    $result = mq($sql);
    if($result){
        alert_msg('성공적으로 등록되었습니다.');
        location_replace('./login.html');
    }
}
?>
```
 `signup.html`을 통해 전달된 값을 기반으로 입력값 공백 확인 및 중복 여부를 검사한다. 

 사용자의 이메일 정보를 `COOKIE`로 저장한 이유는 두 가지이다. 첫번째는 이메일 인증을 위한 php가 따로 있기 때문이다. 이메일 인증 이후의 가입을 유도하기 위해서 두 php 간의 이메일 인증 여부를 공유해야 했다. 두번째는 사용자가 인증번호 발송 이후에 이메일 정보를 수정하는 경우 때문이다. 가령 `a@example.com`으로 인증번호를 전송하고 `b@example.com`으로 인증을 받을까봐 였다. 물론 쿠키값을 임의대로 수정할 수 있기에 이메일 인증 단계에서 db를 통해 검사하는 루틴도 추가되어 있다.

 인증된 이메일 정보인지를 확인하기 위해서 인증을 위한 db로 질의/응답을 진행한다. 사용자가 작성한 이메일 정보 행에 `checked` 컬럼 값이 참인 경우에만 인증된 것으로 간주한다.

 실질적으로 데이터베이스에 정보를 넣는 쿼리는 `INSERT`문을 이용했다. 비밀번호 저장 시에는 `sha2()`를 이용하여 SHA-256 단방향 암호화하여 알아챌 수 없도록 했다. `now()`라는 함수는 mysql 내장 함수로 현재 시간을 나타내는 함수이다.

 가입 이후에는 로그인 홈페이지로 이동된다.

<br><br>

## email_varify.php
```php
<?php
include ('db_conn.php');
$useremail=$_POST['useremail'];
setcookie ( 'useremail' , "$useremail" );

function get_random($num){
    $res = '';
    for($i=0;$i<$num;$i++){
        $tmp = rand(0,9);
        $res .= (string)$tmp;
    }
    return $res;
}

if($_POST['useremail']==NULL){
    alert_msg('이메일 정보를 입력해주세요.');
    history_go();
    exit;
}else{
    $conn=db_connect();
    $random = get_random(6);


    //반영이 안됨.
    //echo "<script>document.getElementById('target_email').value= '$useremail';</script>";
    //echo "<script>document.getElementById('btn_check').disabled = false;</script>";
    $sql = "SELECT createtime FROM varify WHERE useremail='$useremail' ORDER BY createtime DESC LIMIT 1";
    $row = mqr($sql);
    $current_time =  date("Y-m-d H:i:s",time());
    $createtime=$row['createtime'];
    $res=strtotime($current_time)-strtotime($createtime);
    if($res < 180){//3분이 안지난 경우
        alert_msg('재발송은 이전 발송 3분 이후에 가능합니다.');
        history_go();
    }

    //save_db for check
    $sql = "INSERT INTO varify (useremail,createtime,code) VALUES('$useremail',now(),'$random')";
    $result = mq($sql);
    alert_msg('인증코드가 전송되었습니다.\n5분내로 인증해주시길 바랍니다.');
    history_go();

    //send mail
    $to= $useremail;
    $subject = 'Verification from P4Css';
    $message = '
    <html>
    Please put the code below in the verification field on P4Css<br>
    ------------------------<br>
    '.$random.'<br>
    ------------------------<br>

    Please click this link to activate your account:<br>
    </html>
    ';

    $headers = 'From:noreply@example.com' . "\r\n";
    $headers .= 'Organization: Sender Organization ' . "\r\n";
    $headers .= 'MIME-Version: 1.0 ' . "\r\n";
    $headers .= 'Content-type: text/html; charset=utf-8 ' . "\r\n";
    $headers .= 'X-Priority: 3 ' ."\r\n" ;
    $headers .= 'X-Mailer: PHP". phpversion() ' ."\r\n" ;
    //$result=mail($to, $subject, $message, $headers);
    exit;
    
}
?>
```
회원가입 페이지에서 `Send` 버튼을 누르면 동작하는 php이다. `useremail` 정보를 다른 php에서 사용하기 위해 입력한 값을 쿠키로 저장하였다. 이메일 인증에서 사용되는 인증번호는 php의 `rand()`를 이용하여 한자리씩 총 6자리로 구성되어 있다. 반드시 6자리로 구성하기 위해 첫자리 숫자가 '0'이 나오더라도 `(string)`을 통해 문자로 변환시켰다.

이메일 인증에서는 `varify`라는 테이블을 이용하는데 이메일 정보, 코드 발급 시간, 코드, 인증 여부 컬럼이 존재한다. 코드 발급 시간은 5분의 유효 기간을 두기 위해서 php의 현재 시간을 나타내는 `date()`와 문자로 표현된 시간 정보를 계산할 수 있도록 변환하는 `strtotime()`을 이용했다. 두 함수를 이용해 연속적인 인증번호 발급을 막고 5분 이내에 인증해야만 하도록 했다.

메일을 보내기 위해서는 php의 `mail` 함수를 사용했는데 해당 함수는 운영체제의 `sendmail` 기능을 이용한다. 따라서 이를 사용하기 위해서는 조치가 필요한데 이는 [Day13](https://jminis.github.io/docs/P4C/Day13/#php%EC%9D%98-sendmail)에 기록되어 있다.

메일을 보낼 때 HTTP통신의 정보를 입력해주어야 했는데 이는 구글링.

<br><br>

## varify_code
```php
<?php
include ('db_conn.php');
$useremail=$_COOKIE['useremail'];
$input_code = $_POST['input_code'];

if($useremail==NULL){
    alert_msg('먼저 인증코드를 발송해주세요');
    exit;
}else{

    $conn=db_connect();

    //check with db
    $sql = "SELECT code,createtime FROM varify WHERE useremail='$useremail' ORDER BY createtime DESC LIMIT 1";
    $row = mqr($sql);

    $current_time =  date("Y-m-d H:i:s",time());
    $createtime=$row['createtime'];
    $res=strtotime($current_time)-strtotime($createtime);
    $code = $row['code'];
    if($code == $input_code){
        if($res > 300){//5분이 지날 경우
            alert_msg('코드의 유효시간이 만료되었습니다.');
            history_go();
            mysqli_close($conn);
            exit;
        }else{
            $cksql = "UPDATE varify SET checked=TRUE WHERE code='$code' ";
            mysqli_query($conn,$cksql);
            alert_msg('이메일 인증이 완료되었습니다.');
            history_go();
            mysqli_close($conn);
            exit;
        }
    }else{
        alert_msg('인증코드가 올바르지 않습니다.');
        history_go();
    }
    mysqli_close($conn);
}

?>
```
회원가입 페이지에서 `Varify` 버튼을 누르면 수행되는 코드들이다. 가장 마지막에 발송된 인증번호가 인증되도록 쿼리문에서 `ORDER BY createtime DESC LIMIT 1` 조건을 통해 최근 결과 1개만 긁어왔다.

이전 코드 발송과 같은 맥락으로 `varify` 테이블 정보를 기준으로 저장 및 검사를 진행한다.

<br><br>

## logout.php
```php
<?php
include('db_conn.php');
session_start();
$result=session_destroy();
if($result){
    alert_msg('로그아웃 되었습니다.');
    location_replace('login.html');
}
?>
```
로그아웃을 진행할 때 `login.php` 시작한 세션 정보를 지우기 위해서 `session_destory()`를 썼다. 

<br><br>

## main.php

진짜 더러운 코드가 아닐까 싶지만 나름 최선을 다했다 ㅠㅠㅠㅠ  
코드가 길기에 분할하여 설명하겠다.

<br>

```html
<head>
	<meta charset="utf-8">
	<link rel="stylesheet" href="/css/mstyle.css">
	<title>P4CSS</title>
	<style>
	<?php  if(isset($_GET['track'])) echo "#lb_".$_GET['track'];
	else echo "#lb_all"; ?>
	{
		background-color: rgba(16,141,191);
	}
	</style>
</head>
```

먼저 head 부분이다. `meta` 태그로 한글 깨짐 방지, `link` 태그로 css파일을 불러왔다.  
css는 mstyle.css에 저장되어 있지만 `style`을 따로 선언해둔 이유는 게시판이 선택되었을 때 해당 게시판이 선택된 걸 알 수 있도록 하기 위해서 이다. php코드를 통해 현재 보여지고 있는 게시판에 따라 id 선택자 이름이 달라지게 함으로써 적용했다.

`isset`은 값이 존재하는지 확인하는 함수이고, id 선택자는 `#` 태그로 시작된다.

<br>

```
<div id ="header_container">
	<div id="left-header">
		<a href="main.php" style="font-size: 150px">Story Shared</a>
	</div>
	<div id="right-header">
	<p><br/>
		<?php   session_start();  if(isset($_SESSION['username']))  echo $_SESSION['username'];  else location_replace('login.html');?> 님
	</p>
	<a href="logout.php" >LOGOUT</a>
	</div>
</div>
```
페이지의 가장 상단에 위치하는 코드이다.  
`left-header`는 상단의 좌측으로 큼지막한 글자를 나타내고 `right-header`는 우측의 사용자 이름과 로그아웃 링크가 존재한다. 만약 로그인이 되어있을 경우에는 로그아웃 링크가 존재하지만 그렇지 않을 경우에는 로그인 페이지로 이동된다. 이를 판별하는 것이 `login.php`에서 만들어둔 `session_start()` 덕분이다.

<br>

```php
<div id="body_container">
	<div id="left-body">
		<div id="lb_wrap">
			<input id="lb_all" class="load_btn" type="button" value="ALL" onclick="location.href='main.php'"><br/>
			<input id="lb_web" class="load_btn" type="button" value="WEB" onclick="location.href='main.php?track=web'"><br/>
			<input id="lb_sys" class="load_btn" type="button" value="SYS" onclick="location.href='main.php?track=sys'">
		</div>
	</div>
```
메인 페이지에서 게시판 선택을 하는 부분으로 자바스크립트의 `location.href`를 이용하여 트랙별로 글을 볼 수 있도록 했다. 사이트 이동 시 track 인자의 값을 전달함으로써 쿼리문에 트랙을 포함하여 질의한다.

<br>

```php
<div id="right-body">
	<p>
		<form action="search.php" method="get">
		<?php if(isset($_GET['track'])){ $track = $_GET['track']; 
		echo "<input type='hidden' name='track' value=$track>";}?>
		<select style="width: 70px;" name="key">
			<option value="title">제목</option>
			<option value="content">내용</option>
			<option value="writer">작성자</option>
		</select>
		<input type="text" name="value" placeholder="Search">
		<input class="btn_search" type="submit" value="검색">
		</form>
	</p>
	<p style="text-align:right">
		<a href="write.php" style="font-size: 40px;">글쓰기&nbsp;</a>
	</p>
```
게시글을 나타내는 화면에서 제일 위에 있는 것은 검색창이다. `select` 태그를 이용하여 검색할 요소를 선택하고 값을 작성한 뒤에 `Search` 버튼을 누르면 `search.php`가 동작한다.

중간에 php 코드가 작성되어 있는 부분은 게시판 별 검색을 위한 인자 전달부분으로써 `hidden` 속성 처리되었다. 현재 보고 있는 `track`의 게시물 중에서 검색하기 위해선 `track` 정보가 필요하기 때문이었다.

`글쓰기` 버튼을 누르면 write.php가 수행된다.

<br>

```php
<table>
	<thead>
		<th width = "100">번호</th>
		<th width = "200">날짜</th>
		<th width = "800">제목</th>
		<th width = "200">작성자</th>
		<th width = "100">조회수</th>
		<th width = "100">추천</th>
	</thead>
	<tbody>
	<?php
	include('db_conn.php');
	
	isset($_GET['page']) ? $page=$_GET['page']:$page=1;
	$sql = "SELECT * FROM story".(isset($_GET['track'])? " WHERE 	
	track='".$_GET['track']."'" :"" );
	$result = mq($sql);
	$total = mysqli_num_rows($result);

	$list = 5;
	$block_cnt = 5;
	$block_num = ceil($page / $block_cnt);
	$block_start = (($block_num - 1) * $block_cnt) + 1;
	$block_end = $block_start + $block_cnt - 1;
	$total_page = ceil($total / $list);
	if($block_end > $total_page){
	$block_end = $total_page;
	}
	$total_block = ceil($total_page / $block_cnt);
	$page_start = ($page - 1) * $list;

	if(isset($_GET['track'])) {
	$track=$_GET['track'];
	$sql = "SELECT * FROM story WHERE track ='$track' ORDER BY idx 
	DESC LIMIT $page_start, $list";
	}else $sql ="SELECT * FROM story ORDER BY idx DESC LIMIT $page_start, $list";

	$result=mq($sql);
	while($row = $result->fetch_array())//글 제목 길이 수정
	{
		$title=$row['title'];
		if(strlen($title)>20)
	{
	$title=substr($row['title'],0,25)."...";
	$title=mb_strimwidth($row['title'],0,20,"...","utf-8");
	}
	?>
	
	<tr class='<?php echo $row['track']; ?>'>
		<td width="100"><?php echo $row['idx']; ?></td>
		<td style="font-size: 25px;" width="200"><?php echo 
		$row['createtime']?></td>
		<td width="700"><a href="read.php?idx=<?php echo 
		$row["idx"];?>"><?php echo $title;?></a></td>
		<td width="200"><?php echo $row['writer']?></td>
		<td width="100"><?php echo $row['hit']; ?></td>
		<td width="100"><?php echo $row['rmd_cnt']; ?></td>
	</tr>
	<?php } ?>
	</tbody>
</table>
```
전체적인 출력은 `table` 태그를 이용하여 출력하였다. `thead` 태그로 테이블의 속성들을 지정하였고 속성값들은 `tbody` 태그에 속해있다.

```php
isset($_GET['page']) ? $page=$_GET['page']:$page=1;
$sql = "SELECT * FROM story".(isset($_GET['track'])? "
WHERE track='".$_GET['track']."'" :"" );
```
이 코드가 속해있는 이유는 게시판별로 글을 출력하기 위해서이다. 현재 선택한 게시판의 `track` 정보를 `GET` 메소드로 받음으로써 게시글 조회 쿼리를 날릴 때 `WHERE` 절에서 사용된다. 이 `track` 정보를 넘겨주는 것은 이전 id `rigth_body` 태그에서 볼 수 있었다.

그 아래 `$list` 변수로 시작하여 `$page_start` 변수 정보가 작성되어 있는 부분은 페이징 처리를 위한 부분이다. 해당 코드 아래 `if(isset($_GET['track'])) {`로 시작되는 코드는 게시판 별로 페이징 처리를 위해서는 쿼리가 달라서이다. 이 코드가 있어야 게시판별로 리스트 출력 및 검색이 가능해진다.

글 제목 길이라는 주석이 되어 있는 부분은 문자열을 특정 길이로 자를 수 있는 `mb_strimwidth()`를 이용했다. 제목이 아주 길더라도 25byte가 넘어간다면 "..." 으로 생략되어지는 기능이다.

젤 마지막 코드 뭉텅이는 위의 코드들에서 수행된 쿼리 결과를 바탕으로 글의 번호, 제목, 생성날짜 등등이 출력되도록 한다. 글의 제목을 누르면 해당 글을 읽을 수 있도록 `read.php`가 실행된다.

<br>

```php
<div style="font-size:30px">
               <?php
               if ($page > 1) echo "<a href='main.php?".(isset($track) ? "track=".$track."&" : "")."page=1'>처음</a>";
               for($i = $block_start; $i <= $block_end; $i++){
                   if($page == $i){  echo "<b> $i </b>";  }
                   else {  echo "<a href='main.php?".(isset($track) ? "track=".$track."&" : "")."page=$i'> $i </a>"; }
               }
               if($page < $total_page) echo "<a href='main.php?".(isset($track) ? "track=".$track."&" : "")."page=$total_page'>마지막</a>";
               ?>
               </div>
            </div>
        </div>
    </body>
```
결국 main.php 마지막까지 왔는데 페이지를 나타내는 코드이다. 조건 분기를 이용하여 현재 페이지를 나타냄과 동시에 다른 페이지를 선택하면 해당 페이지로 이동할 수 있다.

<br><br>

## write_action.php
```php
<?php
include ('db_conn.php');
session_start();
$writer=$_SESSION['username'];
$track=$_SESSION['usertrack'];
$title=$_POST['title'];
$content=$_POST['content'];

if( !isset ($_SESSION['username'] ) ){
    ?><script>location.replace("main.php");</script><?php
}else if($title == NULL || $content == NULL){?>
    <script>
    alert('제목 또는 내용을 채워주세요.');
    history.go(-1);
    </script>
<?php
}
else{
    // 파일 업로드
    if($_FILES['userfile']['name'] != NULL){
        $uploaddir = '/opt/lampstack-8.1.4-0/apache2/htdocs/upload/';
        $uploadfile = $uploaddir . basename($_FILES['userfile']['name']);
        if (move_uploaded_file($_FILES['userfile']['tmp_name'], $uploadfile)) {
            alert_msg('파일 업로드 성공!');
        } else {
            alert_msg('파일 업로드 실패!');
        }
    }

    $file = $_FILES['userfile']['name'];
    $sql = "INSERT INTO story (createtime, writer, track, title, content,hit,file) values(now(),'$writer','$track','$title','$content',0,'$file')";
    $result = mq($sql);

    if($result){
        alert_msg('글이 등록되었습니다.');
        location_replace('/main.php');
        exit;
    }
    else{
    	alert_msg('오류가 발생했습니다.');
        location_replace('/main.php');
        exit;
}
mysqli_close($conn);

}
?>
```
글 작성을 위한 `write.php`도 존재하긴 하지만 실질적으로 글을 작성할 때 사용되는 php는 `write_action.php` 이다.

글 작성 시에 파일 업로드 기능이 포함되어 있는데 `file` 타입의 데이터를 넘겨받을 경우에는 `$_FILES` 를 사용한다. 해당 데이터 형식도 쿼리 응답 형식과 같이 연관 배열이기에 키-값으로 데이터를 확인할 수 있다. 파일 저장 시에 other에게 폴더 접근 권한을 부여해야 한다.


<br><br>

## read.php

```php
<?php
    include('db_conn.php');
    $idx=$_GET['idx'];
    $sql ="SELECT * FROM story WHERE idx='$idx'";
    $row=mqr($sql);

    if(!isset($_COOKIE['readNo_'.$idx])) {
        $hit=$row['hit']+1;
        $hitsql = "UPDATE story SET hit = '$hit' WHERE idx = '$idx'";
        $hitquery=mq($hitsql);
        setcookie("readNo_".$idx, $idx, time() + 60 * 60);
    }
?>
```

글 정보를 가져오는 쿼리와 조회수 정보를 반영하기 위한 쿼리이다. 이 때 쿠키 정보를 이용하여 1 시간 마다 조회수가 갱신되도록 하였다. `setcookie()`는 쿠키명,데이터,시간을 지정하여 쿠키 정보를 저장한다.



```php
<div id="cmt_form">
    <div id="cmt_count">댓글
        <span id="count">0</span>
    </div>
    <form class="" action="add_cmt.php" method="post">
        <div>
            <textarea name="content" placeholder="Input your comment."></textarea>
            <input type="hidden" name="story_idx" value=<?php echo $idx;?>>
            <input type="submit" id="cmt_submit" value="남기기">
        </div>
    </form>
    <div id="cmt_row">
        <table >
            <thead>
                <th width = "1000">내용</th>
                <th width = "200">작성자</th>
                <th width = "200">날짜</th>
                <th width = "100"></th>
            </thead>
            <tbody>
                <?php
                $sql ="SELECT * FROM comment WHERE story_idx='$idx'  ORDER BY idx desc";
                $result = mq($sql);
                    while($row = $result->fetch_array())
                    { ?>
                <tr>
                    <td width="1000"><?php echo $row['content']; ?></td>
                    <td width="200"><?php echo $row['writer']?></td></td>
                    <td width="200" style="font-size:20px"><?php echo $row['createtime']?></td>
                    <td width="100"><?php if($row['writer'] == $_SESSION['username']){ ?>
                        <a href="del_cmt.php?idx=<?php echo $row['idx'];?>" style="font-size: 20px;">삭제</a>
                        <?php } ?></td>
                </tr>
                <?php } ?>
            </tbody>
        </table>
    </div>
  </div>
```

글 내용을 불러오는 과정은 `SELECT` 문이기에 생략했고 게시글 아래 쪽에 존재하는 댓글과 추천 기능과 관련된 코드이다. 댓글을 위해 `commet` 테이블을 만들었고, `story_idx` 컬럼을 이용해 부모 글이 누군지 지목할 수 있도록 하였다. 

```php
<td width="100"><?php if($row['writer'] == $_SESSION['username']){ ?>
<a href="del_cmt.php?idx=<?php echo $row['idx'];?>" style="font-size: 20px;">삭제</a>
```
또한 댓글 작성자가 현재 로그인한 사용자라면 해당 댓글을 삭제할 수 있도록 되어 있다.

<br>

```php
<a href="recommend.php?idx=<?php echo $idx;?>" style="font-size: 40px;"><br>추천하기</a>
```
가장 마지막 줄에는 추천을 위한 코드가 포함되어 있다. 추천을 위한 테이블은 `recommend`이다.

<br><br><br>

그 외 php 정보들은 중복되는 내용이 많기에 생략하겠다.  
다음은 서버 호스팅 이후에 진행해야 겠다.