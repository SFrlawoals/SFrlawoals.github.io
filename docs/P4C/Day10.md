---
layout: default
title: Day10
nav_order: 3

parent: Develop Project - W2
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 대문 구현하기

정말 간단할 줄 알고 붙잡았는데 생각보다 너무 많은 시간을 썼다.  
진짜 단순히 왼쪽/오른쪽으로 구성하여 넣으면 그만이겠거니 했는데,  
볼 때는 진짜 쉬워보였는데 직접 만드니까 한 세월이 걸린다.  
근데 그 뿌듯함이란...

![image-20220427143506145](../img/image-20220427143506145.png)

재밌는 건 확대나 축소하면 다 깨진다는 것이다... flex를 어떻게 해야하는지 참...  
만족은 못하지만 이제는 기능 구현에 집중해야겠다..

```css
@charset "UTF-8";
@font-face {
    font-family: 'LeeSeoyun';
    src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_2202-2@1.0/LeeSeoyun.woff') format('woff');
    font-weight: normal;
    font-style: normal;
}
a { text-decoration: none; color: #000; }
* {
    font-family: 'LeeSeoyun',sans-serif;
}
.body-container {
    height: 500px;
    display: flex;
}
.left-container{
    background-color: white;
    text-align: center;
    margin-top: 5%;
    width: 50%
}
.right-container{
    background-color: white;
    text-align: center;
    margin-top: 10%;
    flex-grow: 1;
}
form input:not(input[type='submit']){
    border: 1px solid #d9d9d9; width: 400px; height: 50px;
    margin: 5px; padding-left: 10px; border-radius: 5px;
    box-sizing: border-box; font-size: 18px; 
}
form input[type='submit']{
    border: 0px; width: 400px; height: 50px;
    margin: 5px; padding-left: 10px; border-radius: 5px;
    background: linear-gradient(to right, #13ABE8, #acd1df);
    color: #fff; font-weight: bold; font-size: 18px;
    cursor: pointer;
}
```

특정 type에만 설정을 하고 싶었는데 구글링은 모든 것을 해결해주었다.  
기본 폰트는 섭할 것 같아서 폰트도 찾아 넣어보았다.

###### [https://velog.io/@timcodejs/PHP-MySQL-로그인-및-회원가입-폼-만들기-1](https://velog.io/@timcodejs/PHP-MySQL-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EB%B0%8F-%ED%9A%8C%EC%9B%90%EA%B0%80%EC%9E%85-%ED%8F%BC-%EB%A7%8C%EB%93%A4%EA%B8%B0-1) 를 많이 참조했다.
<br><br><br>

-----

# > 회원 가입과 DB 연동

로그인 페이지를 만들고 나니 회원 가입 페이지는 어렵지 않았다.   
회원 가입을 수행할 php 코드도 내친김에 만들었는데 만들다 보니 이상했다.

```php
<?php
$uemail = $_POST['useremail'];
$uid = $_POST['userid'];
$upw = $_POST['userpw'];
$upwc = $_POST['userpw_ck'];
$uname = $_POST['username'];
$utrack =  $_POST['usertrack'];
error_reporting( E_ALL );
ini_set( "display_errors", 1 );

$conn = mysqli_connect("localhost","root","","my_web") or die ("Can't access DB");
$sql = "SELECT userpw FROM userinfo WHERE userid='$uid'";
$sql = "INSERT INTO userinfo (useremail,userid,userpw,username,usertrack,createtime) 
VALUES('$uemail','$uid','$upw','$uname','$utrack',now())";

$result = mysqli_query($conn, $sql);
if($result){
    echo "''$uname'님 성공적으로 가입되었습니다.";
}else {
    echo "오류가 발생했습니다.";
}

?>
```

테스트를 진행하던 중에 
1. 사용자가 내용을 다 채우지 않으면 어떡하지?  
2. 중복된 아이디는 어떻게 검사하지?  
3. 비밀번호 확인이 올바르지 않을 때 재 입력 요구를 어떻게 하지?  
4. 회원 가입 성공 후 어떻게 다시 로그인 사이트로 돌려보내지?  

이 문제를 해결하기 위해 기쁨도 잠시 다시 공부하러 떠날 예정이다.  
그전에 기념샷 정도는 괜찮잖아...

![image-20220427171221444](../img/image-20220427171221444.png)

MySQL에 접속하여 확인해보니 등록도 되어 있었다.

![image-20220427170702009](../img/image-20220427170702009.png)

<br>

추가로 bitnami를 이용하여 구축된 환경에서 php 오류 메세지가 기본적으로 출력되지 않도록 설정되 어 있다. 따라서 `php.ini`를 수정하거나 아래 코드를 문서에 넣어두어야 디버깅이 수월하다.

```php
error_reporting( E_ALL );
ini_set( "display_errors", 1 );
```



<br><br><br>

-----

# > 생활 코딩과 공부하기

(https://opentutorials.org/course/3085/18778)[https://opentutorials.org/course/3085/18778] js

## 시작하기
php처럼 js도 프로그래밍적 기능을 수행할 수 있는 언어이다.  
그렇기에 정적인 HTML에 동적인 기능을 `JavaScript`로 만들 수 있다.

```html
<input type="button" onclick="alert("hi")">
<script>alert(document.cookie());</script>
```

보다시피 HTML의 속성값으로 자바스크립트의 함수를 사용할 수 있고,  
`<script>` 태그를 넣음으로써 자바스크립트를 실행할 수 있다.  
또한 `onclick`과 같은 속성을 **이벤트**라고 하는데 이러한 속성들에 반응을 추가할 수 있다.

자바스크립트도 언어이기에 함수를 만들어 사용할 수 있는데 HTML에 삽입할 수 있다.
```html
<script>
function check(){
	var uemail = document.getElementById("useremail").value;
	if(uemail)  //userid로 받음
	{
		url = "check.php?useremail="+uemail;
		window.open(url,"chk","width=400,height=200");
	} else alert("이메일을 입력하세요.");
}
</script>
```
변수는 `var`를 이용하여 지정하고 php와 달리 변수들 앞에 `$`기호를 붙일 필요없다.  
`window.open()`의 문법은 window.open(url, name, specs, replace) 이다.  
자바스크립트를 낑겨 넣음으로써 또 다른 php호출을 할 수도 있겠다.

<br>
```html
<script src="/js/check.js"></script>
```
추후 HTML에 위 정보를 삽입하면 외부 자바스크립트 파일의 함수를 끌어다 쓸 수 있다. 

<br>
또 php에서 자바스크립트 코드를 이용해 페이지를 넘어가는 방법을 찾아 헤맸는데 퍼왔다.  
아니 생각보다 구체화 할수록 어렵다 ㅠㅠㅠㅠㅠ
```php
echo "<script>location.href='URL 또는 경로'</script>";
echo "<script>location.replace('URL 또는 경로')</script>";
header("location: URL 또는 경로");
echo "<meta http-equiv='refresh' content='시간' url='URL 또는 경로'";
```
요렇게 뾰로롱