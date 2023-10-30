---
layout: default
title: XSS(Cross Site Scripting)
nav_order: 1
parent: Web Hacking
---



# XSS(Cross Site Scripting)

> Client Side 취약점 중 하나로 웹 리소스에 스크립트를 삽입함으로써 사용자의 웹 브라우저에서 세션 탈취와 같은 악의적인 행위를 실행하는 기법이다.





## Q. 왜 발생하는걸까?

\> HTML에는 다양한 기능을 수행하기 위해 여러 태그가 존재한다. 이 중 <script> 태그는 Java Script 를 의미하며, 사용자 데이터 입력 및 전송 등의 이벤트를 발생시킬 수 있다. 또한 웹 문서에서 사용자의 태그 입력 및 내용을 검사하는 루틴이 없기에 Java Script 등을 이용한 악의적인 코드가 삽입되더라도 실행되어지기 때문이다

<br><br><br>

## Q. 어떻게 막아야할까?
\> <span style="color:yellow">SOP(Same Origin Policy)</span>와 같은 보안정책이 등장하였지만 이를 우회하는 기술이 소개되고 있는 추세이다. 때문에 적절한 필터링과 보완된 라이브러리를 사용해야 한다.

[^SOP]: scheme, host, port 모든 요소가 일치될 경우에만 정보 출처의 origin으로 인식한다

<br><br><br>

## > 3 Type of XSS: 종류별로 알아보자

1. Stored XSS
2. Reflected XSS
3. DOM-based XSS

<br><br><br>

## > XSS Filtering Bypass: 만만한 필터링을 뚫어보자

1. 이벤트 핸들러
   onload : 요청 데이터 로드 성공 시 실행  
   onerror : 요청 데이터 로드 실패 시 실행  
   onfocus : 포커스 발생 시 실행  
   ex) `<img src=0 onerror="alert(1);">`

   <br>

2. 활성 하이퍼링크
   "javascript: " URL 로드 시 자바스크립트 코드 실행  

   ex) `<a href="javascript:alert(document.domain)">Click me!</a>`

   <br>

3. 

