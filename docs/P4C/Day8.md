---
layout: default
title: Day8
nav_order: 1

parent: Develop Project - W2
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 인증 페이지 만들기

코드를 작성하기 앞서 내가 생각한 로그인 화면은  메인화면에 로그인 기능이 있는 것이 아닌  
로그인 이후에만 메인 페이지를 확인할 수 있는 폼을 생각했다.

가장 첫 화면에는 로그인 폼이 존재하고,   
아래 회원가입으로 이동가능한 버튼을 만들 예정이다.

## login.html
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>P4CS</title>
    </head>
    <body>
        <header>
            WELCOME to " P4C Shared "
        </header>
        <form  action="login.php" method="post">
            <p>아이디 <input type="text" name="userid" placeholder="Input your ID"></p>
            <p>비밀번호 <input type="password" name="userpw" placeholder="Input your PW"></p>
            <input type="submit" value="로그인">
        </form>
            <p>아직 가입을 안했다면? <a href="signup.html">가입하기<a></p>
    </body>
</html>
```
막상 폼을 만들어 놓고 보니 도저히 디자인을 못봐주겠다는 생각이 들었다.  
그래서 css와 관련하여 조금 찾아보니 class에 대한 개념이 markdown 사용할 때랑 비슷하여  
일단 기본적인 폼부터 집고 넘어가기로 했다.

<br>

## login.php
```php
<?php
$uid = $_POST['userid'];
$upw = $_POST['userpw'];
$conn = mysqli_connect("localhost","root","","my_web") or die ("Can't access DB");
$sql = "SELECT userpw FROM userinfo WHERE userid='$uid'";

$result = mysqli_query($conn, $sql);
$row = mysqli_fetch_array($result);

if($row['num_rows'] == 1){
    if($row['userpw'] === $upw){
        echo("로그인 성공!!!");
    }else echo("로그인 실패...");
}else echo("로그인 실패...2");

?>
```
첫 주차에서 php와 mysql에 대해 조금 배웠었지만 두 기능을 연동하여 사용해보지 못했었다.  
그러나 사용자 정보를 코드에 담아둘 수 없기에 데이터베이스 연동 방법이 궁금했었는데  
찾아보니 웹 전용 언어답게 php는 MySQL 연동을 위한 함수가 존재하였다.

`mysql_connect`는 db에 접속하기 위한 함수로 서버, 계정명, 비밀번호, 데이터베이스명 을 인자로 전달받는다. 처음에 내 환경에서 PATH가 Bitnami 폴더로 지정되어 있지 않다는 사실을 잊고 있다가 도저히 접속이 되지 않아 뇌절을 거듭한 끝에 기억해냈다. 

`mysqli_query`는 db에 쿼리 요청을 통해 응답을 받아오는 함수로 `mysql_connect`에 성공하였을 때 반환되는 연결 핸들과 쿼리문을 인자로 전달하면 그에 대한 응답을 반환한다.  
그리고 그 반환값을 배열로 받아주는 것이 `mysqli_fetch_array`인데 해당 함수를 사용하지 않을 경우  
아래의 ①처럼 출력된다. ②는 함수를 이용했을 때의 반환값이다.

![image-20220425174620102](../img/image-20220425174620102.png)

해당 함수를 이용하여 쿼리 결과문을 배열로 변환한 뒤에 쿼리 결과문 갯수를 의미하는 `num_rows`와 쿼리문에서 포함한 컬럼명을 이용하여 조건문의 참과 거짓을 판별하였다.  
정말 가벼운 비밀번호 확인만 하는 코드도 직접 만드려니 조금 어려웠고 생각보다 고려해야 할 점이 많아서 신경써야 할 부분들을 정리 해보기로 했다.

- [ ] 비밀번호 DB 저장 시 비밀번호 암호화
- [ ] 미 로그인 사용자에게만 로그인 페이지 띄우기
- [ ] 가입 시 이메일 인증 이후에 정보란 띄우기
- [ ] 로그인 시 DB 저장된 비밀번호 복호화하여 인증



<br><br><br>

-----

# > (알)코(올)딩

![image-20220426002559590](../img/image-20220426002559590.png){: width="400"}

요즘 핑계 대면서 시간을 이상한데 쓰는 곳이 있는데,  
아니 비도 오고 그래서 생각을 해보니 일단 코딩을 해야겠다.

<br>

![image-20220426002157789](../img/image-20220426002157789.png)

대충 머리 속에 이러한 구상으로 페이지를 만들어야 겠다는 생각이 확고히 들었다.  
가장 먼저 로그인을 하지 않으면 위 그림처럼 페이지가 뜬다.

기본적으로는 왼쪽의 로그인 폼이 뜨고, 회원가입을 누르면 오른쪽 폼이 뜨는거다.  
또한 누르지 않을 경우 해당 영역은 다른 이미지나 글로 채워지는 것인데,  
어... 할 수 있을지는 모르겠는데 뭔가 할 수 있을 것 같은 알 수 없는 생각이 든다.

다짜고자 로그인 폼을 만든 사람들의 css를 알아보기 시작했다.  
Cascading Style Sheets의 약자로 "어떻게 보이는가"를 정의하는 스타일 시트 언어라고 한다.  
처음에는 왼쪽과 오른쪽을 나누기 위해 분할하는 css를 알아보았는데,  
생각해보니까 css파일과 php&html 파일도 분할되어 있던 것이 생각났다.  

그래서 php&html에서 css파일을 참조하는 방법이 궁금했는데 찾아보니 아래 코드였다.

```html
<link rel="stylesheet" href="css/style.css">
```

물론 보통은 html 파일 안에 `style` 태그를 이용하여 적용할 수도 있다.

```html
<style>
  h1 {
    color: blue;
  }
</style>
```

이런 방식이 있다고는 하지만 나는 위의 방식을 이용하여 다양한 style을 적용할 것이다.

위의 코드가 무슨 뜻 인고 찾아보니 `link` 태그의 `rel` 속성을 통해 해당 문서와 외부 리소스 사이의 관계를 명시한다고 한다.  해당 코드를 통해 style.css의 class들을 참조한다는 뜻인 것 같다. 그럼 이제 html에서 마음껏 class들을 참조하여 html을 꾸밀 수 있는 것이다.

일단 만들고나서 찾아보니 css 파일을 만들어보기에 앞서 `@charset "utf-8"`을 작성하고 보란다.  
그 이후에 사용되어 지고 있는 태그 또는 특정 클래스명을 만들어 작성하는 형식이라고 한다.

그래서 왼쪽과 오른쪽을 구분하기 위한 폼을 한번 작성했다.

```css
@charset "UTF-8";

.left-space{
    background-color: white;
    float: left;
    width: 50%
}

.right-space{
    background-color: white;
    float: right;
    width: 50%
}
```

구글링을 통해서 찾아보다 보니 또 클래스명 앞에 `.` 기호와 `#` 기호가 붙는 경우가 있었는데,  
나는 무지하게도 css에 포함되어 있는 것들은 그저 클래스라고 생각을 했었다.  
그런데 알고보니 용어에 대한 차이가 있었고 지금 이해한 바로는 다음과 같다.

`#` 기호는 id 값으로 하나의 태그를 지정하여 꾸밀 때 사용할 수 있다.   
`.` 기호는 CSS 선택자로 설정된 태그들을 모두 꾸밀 때 사용할 수 있다.  
그렇기에 `#` 을 이용하여 설정한 값은 하나의 태그에서만 `id` 속성을 이용하여 적용하는 것이고,  
`.` 을 이용하여 설정한 값은 여러 태그에서 `class` 속성을 이용하여 적용하는 것이다.

ID -> 한번만 사용하는 설정  with `#`  
CLASS -> 여러번 사용하는 설정 with `.`  
이렇게 정리해볼 수 있겠다. 뭐 일단 위 코드를 내가 만들어 두었던 signup.html에 적용을 해보았다.

![image-20220426010837386](../img/image-20220426010837386.png)

아주 만족스럽게도 회원가입 폼이 오른쪽으로 이동했는데 다음 번에는 입력창을 꾸며 봐야겠다.  
또한 login.html에서 회원가입을 눌렀을 때 해당 페이지가 자연스럽게 대비되도록 만들 것이다.  
내리는 빗소리를 들으면서 코딩하는게 너무나 낭만적이다. 그렇지 않나?
