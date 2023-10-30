---
layout: default
title: Day2
nav_order: 2

parent: Develop Project - W1
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 구체적인 계획 정하기

프로젝트의 최종 목표인 웹사이트를 만들어내려면 기획,구상,개발,수정의 단계가 필요하다고 생각하여 조금 이르지만 가까운 목표부터 정함으로서 프로젝트 진행 효율을 높이고자 한다.  

로그인이 필요하며 게시판, 추천, 댓글과 같은 기능이 들어가야 하기에 보편적인 카페가 떠올랐다.  
이번 **빡공팟 교육생들 개개인의 주차별 과제 수행 결과물을 서로 공유하는 사이트**가 어떨까 생각을 했고, 과제 결과물의 추천수에 따라 인기 과제물도 선정되는 기능이 포함되면 좋을 것 같다.

<br><br>

1. 메인 화면에서 로그인을 요구하며, 로그인이 되지 않은 사용자는 게시물 내용을 확인할 수 없다.
2. 회원 가입 시 이메일 인증이 필요하며, 이메일을 이용한 비밀번호 찾기 기능이 존재한다
3. 게시판은 주차별 게시판이 존재하며, 상단엔 전체 검색 기능과 하단엔 개별&내용 검색 기능이 존재한다.
4. 글마다 추천 기능과 댓글 기능이 존재하고 방문자 수가 기록되며, 조회수는 1시간에 최대 1번이다
5. 메인 화면에서 추천에 따른 인기 게시글을 확인할 수 있다

<br><br>

가장 먼저 로그인 폼을 만드는 것을 우선 목표로 하며,   
UI와 같은 디테일한 부분은 `css` 강의를 조금씩 챙겨보며 기본적으로만 갖추어둘 예정이다. 

<br><br><br>

-----

# > 생활 코딩과 공부하기

- [https://opentutorials.org/course/2039/10941](https://opentutorials.org/course/2039/10941) (HTML)



## 주요태그 맛보기

```html
<html>
    <head><meta charset="utf-8"></head>
    <body>
        <p>첫번째 단락</p>        
        <p>두번째 단락</p>

        두 번<br><br>줄바꿈<br>

        <img src="./cats.png" height="400" alt="깨진 이미지" title="고양이"><br>

        <table border="2">
            <thead>
                <tr>
                    <th>이름</th><th>성별</th><th>나이</th>
                </tr>
            </thead>

            <tbody>
                <tr>
                    <td>짱구</td><td>남</td><td>5</td>
                </tr>
                <tr>
                    <td>짱아</td><td>여</td><td>1</td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <td colspan="2">합계</td><td>6</td>
                    
                </tr>
            </tfoot>
        </table>

    </body>
</html>
```

HTML만 이용해서 표현 가능한 태그들 중에서 몇 가지를 알아보았다.

`<p>` 태그는 pargraph 의 약자로 문단을 표현하며 문장의 끝에 2번의 줄바꿈이 적용된다.  
`<br>` 태그는 break의 약자로 1번의 줄바꿈을 의미한다.  
위 두 태그는 void element 태그라고 하며 속성이 필요하지 않다.

`<img>` 태그는 이미지를 삽입할 때 사용하는 태그이며 아래 속성들이 사용된다.  
`src` 속성은 source의 약자로 지정할 이미지 정보를 선택한다.  
`alt` 속성은 alternative text로 이미지가 깨졌을 시 출력되는 텍스트이다.  
`title` 속성은 이미지를 선택했을 때 확인되는 텍스트이다.  
`<height>` 와 `<width>` 속성을 이용하여 높이와 너비를 지정할 수 있다.

생활코딩 선생님이 추천해주셨는데 pixabay.com 사이트를 이용하여 스폰 이미지 외 free 이미지를 사용하면 저작권 문제없이 사용할 수 있다고 하였다.

`table` 태그는 표를 만드는 태그로 아래 태그들이 중첩되여 사용된다.  
`<tr>` 태그는 table row의 약자로 표의 열을 의미한다.  
`<td>` 태그는 table data의 약자로 표의 행 데이터를 의미한다.  
`<th>` 태그는 table head의 약자로 헤더 행을 의미한다.  
이 외 `<thead>` 와 `<tbody>`,`<tfoot>` 태그는 해석을 돕기 위한 태그로 선택이다.   
`border` 속성은 표의 테두리 속성이며, `colspan`은 행 병합 속성이다.

아래는 실습 결과물이다.

![image-20220420121538643](../img/image-20220420121538643.png)

<br>

오늘은 눈이 아파서 조금 일찍 껐다.

<br><br><br>

-----
# > 한번 알아보았습니다.

- 어떤 사이트에서는 개행을 `<br>` 로 알려주고 어떤 사이트에서는 `<br/>` 로 알려주었는데, 둘 다 결과물이 동일하게 나와 의미가 있는지 알아보았다.  
  `<br>` 태그는 HTML4부터 지원가능하며, HTML5에서는 선호하고 있다.  
  `<br/>` 태그는 HTML5부터 지원가능하며, XHTML에서 선호하고 있다.  
  따라서 현 시점에서는 어떤 것을 사용해도 상관은 없지만 나는 통일하여 `<br>` 로 사용할 예정이다.

- 위 결과를 찾다보니 XHTML이 무엇인지 궁금했는데 XHTML 은 HTML5 이전의 HTML 표준으로 좀 더 엄격한 문법이 적용된 마크업 언어였다고 한다. 태그는 반드시 소문자이며 태그의 열림과 닫힘 필수와 같은 규칙을 지켜야한다고 한다. 








