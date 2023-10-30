---
layout: default
title: Day3
nav_order: 3

parent: Develop Project - W1
grand_parent: P4C
---
##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 생활 코딩과 공부하기

오늘은 로그인 폼 완성이 목표이다.

우분투에서 진행을 하다보니 html 에디터가 필요했는데,  
생활코딩 선생님 추천의 `atom` 에디터를 설치하여 사용했다.  
`atom` 에디터에서 한글이 입력이 안되어 글꼴을 `NaumGothicCoding` 로 바꾸고,  
또한 한/영 키가 먹질 않아 찾아보니 shift+space로 전환되었다.  

## Form

```html
<!DOCTYPE html>
<html>
    <head>
      <meta charset="utf-8">
    <title>
      로그인 창
    </title>
  </head>
  <body>
    <form action="http://localhost/login.php" method="get" enctype="multipart/form-data">
        
      <p>아이디: <input type="text" name="id" value="Input your id"></p>
		
      <p>비밀번호: <input type="password" name="passwd" value="Input your password"> </p>
        
      <p>트랙: <select name="track">
        <option value="web">웹 해킹</option>
        <option value="system">시스템 해킹</option>
      </select></p>
        
      <p>남: <input type="radio" name="gender" value="man">
            여: <input type="radio" name="gender" value="woman">
      </p>
        
      <p>빨강: <input type="checkbox" name="color " value="red">
          파랑: <input type="checkbox" name="color" value="blue">
          검정: <input type="checkbox" name="color" value="black" checked>
      </p>
        
      <p>
        프로필 업로드 <input type="file" name="profile"><br><br>
      </p>
        
      <label> 비고
        <textarea id= "text-area" rows="2" cols="80">
            이 태그는 기존 value 속성 값과 달리 여러줄 넣을 수 있게 된다</textarea>
      </label><br><br>
        
      <input type="hidden" name="hide" value="bush">
      <input type="button" value="버튼" onclick="alert('메롱')">
      <input type="reset" value="초기화">
      <input type="submit">
        
    </form>
  </body>
</html>

```
`form` 태그는 입력양식들을 이용해 입력받은 값들을 전달할 때 사용되는 태그이다.  
`action` 속성을 이용하여 입력값들을 전달할 목적지를 정할 수 있고,  
`method` 속성을 이용하여 전달 방식을 선택할 수 있다.  
방식에는 `get ` 방식과 `post` 방식이 존재하는데 to be continue

`input` 태그를 이용하여 다양한 타입의 입력 포맷을 만들 수 있다.  
`type` 속성에 `text`를 작성하면 문자를 입력받을 수 있고,  
`password`를 이용해 입력받은 문자들을 '\*'로 보이도록 한다.  
또한 단일선택이 가능한 `radio`, 다중선택이 가능한 `checkbox`,  
파일을 선택하는 `file`, 값을 숨겨 전달하는 `hidden`,  
버튼 형식의 `button`, 선택된 값들이 삭제되는 `reset`  
이 외 많은 타입들이 존재하기에 필요에 따라 찾아서 사용하면 된다.

`select` 태그에 `option` 태그를 중첩하여 아래로 펼쳐지는 리스트를 만들 수 있다.  
다중 선택 기능을 넣을 수 있지만 사용자의 입장에서 불편하기에  
다중선택이 되는 다른 타입의 폼을 사용하는게 좋다.

`label` 태그는 다른 태그를 묶음으로써 코드의 가독성을 높인다.  
레이블 지정을 통해 아래 태그들이 어떤 목표를 수행하기 위해 작성되었는지,  
그리고 레이블 글자를 눌렀을 때 레이블에 속한 폼이 선택되는 기능이 있다.

많은 태그들의 속성에서 `name` 속성과 `value` 속성을 확인할 수가 있는데,  
쉽게 생각해서 `name`은 컬럼명, `value`는 데이터로 생각하면 편하다.  
추후 데이터를 넘길 때 [name]=[value] 형식으로 넘어간다.<br>



우분투 환경에서 코드를 작성하여 사이트에서 확인한 결과물이다.

![image-20220420180602846](../img/image-20220420180602846.png)

추가로 `method`를 `get` 으로 지정했을 경우 아래와 같이 주소창에 보인다.

![image-20220420180708129](../img/image-20220420180708129.png)

<br><br>

## 서버와 클라이언트

필연적으로 php에 대한 공부도 필요해져서 추가로 공부했다.  
[https://opentutorials.org/course/62/233](https://opentutorials.org/course/62/233)

내가 Bitnami를 이용하여 설치한 LAMP에서 AMP는 Apache, Mysql, Php 라는 사실은 알고 있었다.  
하지만 이 구성들을 묶어 표현할 단어 선택을 하지 못했는데, 이를 프래임(Frame)이라고 한다.

데이터를 요청하는 클라이언트는 곧 우리와 같은 사용자가 되는 것이고,  
요청한 데이터를 응답하는 서버는 곧 서비스를 제공하는 측의 서버 장비이다.  
그리고 데이터를 요청하기 위해서 우리는 웹 클라이언트가 필요한데,   
이것이 우리가 잘 알고 있는 크롬과 같은 웹 브라우저이다.

웹 클라이언트(웹 브라우저)는 사용자가 입력한 주소를 따라 서버에 요청을 보내고  
보낸 요청에 대한 응답인 HTML을 볼 수 있도록 번역해 줄 뿐이다.

웹 서버는 초기 웹 클라이언트로부터 HTML 요청을 받을 경우 Apache와 같은 서비스를 통해 응답하였다. 그러나 정적으로만 표현이 가능하며 갈수록 더 많은 정보를 표현해야하는 문제에 직면하였고 이를 해결하기 위해 CGI(common gateway interface)가 등장하였다. HTML 이외에 php, js와 같은 동적인 프로그래밍을 지원하는 언어들을 해석하기 위한 중간다리의 역할을 하며 더 많은 정보를 웹페이지 안에 담아낼 수 있게 되었다.

그리하여 결과론적으로 웹 클라이언트가 웹 서버에 php를 요청할 경우,  
웹 서버의 Apache가 CGI를 통해 php engine과 소통하여 결과값을 도출하고,  
이를 다시 사용자의 웹 클라이언트로 HTML를 전송한다.  
그리고 우리는 웹 클라이언트에 의해 해석되어진 HTML을 확인할 수 있다.

<br>
![Whiteboard](../img/Whiteboard.png)

끄적...