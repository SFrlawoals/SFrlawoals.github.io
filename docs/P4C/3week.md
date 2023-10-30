---
layout: default
title: Develop Project - W3
nav_order: 3

has_children: true
parent: P4C
---



##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

# W3: [개발프로젝트] HTML, PHP, MySQL, js 공부 (CSS)

<br>

- 블로그에 개발 진척도에 관한 설명글 작성
- 회원가입
- 로그인
- 메일 인증
- 게시판: 글쓰기, 댓글, 추천, 파일 업로드
- 검색: 제목 기준, 게시판 별 검, 전체 기준 검색, 내용 기준 검색 
- 방문자 조회수: 무한f5 방지용 1시간 마다 갱신 가능하게
- UI  조금은 신경 써야 함
- 블로그에 개발 완성도에 관한 설명글 작성

<br><br>

-----

# > 설명글

**완성된 웹 사이트 - [p4c4s.ml](http://p4c4s.ml)** 

**작성한 코드 - [https://github.com/Jminis/P4C](https://github.com/Jminis/P4C)** 

<br>

**3주라는 시간동안 부족하지만 만든 결과물입니다.**

<br>

login.html

![image-20220505161126565](../img/image-20220505161126565.png)

<br>

main.php

![image-20220505161207683](../img/image-20220505161207683.png)

<br>

search.php

![image-20220505161240711](../img/image-20220505161240711.png)

<br>

read.php

![image-20220505161324683](../img/image-20220505161324683.png)

<br><br>

코드는 "생활코딩"을 통해 틀을 만들고 "구글링"으로 살을 채워갔습니다.  
코드 작성에 대한 과정과 설명은 [Day15](http://localhost:4000/docs/P4C/Day15/#-php-%EC%A0%95%EB%A6%AC)에 정리했습니다.  

<br><br>

## 리뷰

과제 요구 사항 만족도는 아래와 같습니다.

1.회원가입 기능 구현
{: .text-green-000}

2.로그인 기능 구현
{: .text-green-000}

3.이메일 인증 기능 구현(로컬 서버)
{: .text-green-000}

4.게시판 글쓰기, 댓글, 추천, 파일 업로드 구현
{: .text-green-000}

5.게시판 별 검색 기능 구현
{: .text-green-000}

6.페이지별 미숙한 CSS 적용
{: .text-yellow-000}

<br>

추가적으로 적용한 기능은 아래와 같습니다.  
1.글 삭제 기능 구현 

2.댓글 삭제 기능 구현


<br>

부족한 기능은 아래와 같습니다.  
1.이메일 인증 불가(외부 서버)  
{: .text-red-000}

2.비밀번호 찾기 불가
{: .text-red-000}

<br>

AWS EC2 인스턴스에서는 SMTP(25번 포트)를 기본적으로 제한한다고 합니다.  
로컬 서버에서는 이메일 인증을 진행하였으며 이에 대한 기록은 [Day13](https://jminis.github.io/docs/P4C/Day13/#-%EC%84%B1%EC%9E%A5-%EA%B3%BC%EC%A0%95)과 [2week](https://jminis.github.io/docs/P4C/2week/)에 나와있습니다.  
비밀번호 찾기는 이메일 인증이 외부 서버에서 불가능하여 생략하였습니다.  

<br>

2주차의 마지막 계획과는 다르게 진행이 되면서 주춤하는 시간이 있었는데  
결과적으로 이렇게 마무리를 하게 되었습니다.  
결과가 아쉽지만 재밌었고 다음 과제도 얼른 하고 싶습니다.

-----


{: .toc}