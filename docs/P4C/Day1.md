---
layout: default
title: Day1
nav_order: 1

parent: Develop Project - W1
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 생활 코딩과 공부하기

- [https://opentutorials.org/course/2039](https://opentutorials.org/course/2039) (HTML)



## HTML이란?

HTML: HyperText Markup Language  
하이퍼텍스트를 가장 중요한 특징으로 하는,  
마크업이라는 형식을 가진  
컴퓨터 프로그래밍 언어

웹을 만들어달라는 의미의 언어로 컴퓨터와 사람간의 소통이 가능하도록 만든 약속의 집합체이다.  
그리고 이를 읽어주는 것이 웹 브라우저로 Chrome과 같은 프로그램이 이에 해당한다.  
"팀버너스리" 라는 인물에 의해 만들어졌으며 현재 우리가 사용하고 있는 HTML은  
GML -> SGML -> SGMLguid 를 거쳐 현재의 HTML이 탄생하게 되었다.  
점점 더 Semantic해지기 위해 발전해나가고 있으며 최근 표준은 HTML5 이다.

<br>

## 태그와 속성 맛보기
태그와 속성은 HTML에서 특정한 의미를 부여하는 기능을 수행한다.  


### a.html

```html
<h1>오늘의 명언</h1>

우리 모두는 <strong>자신의 힘</strong>으로 발견한 내용을 가장 쉽게 익힌다.
(<a href="https://ko.wikipedia.org/wiki/%EB%8F%84%EB%84%90%EB%93%9C_%EC%BB%A4%EB%88%84%EC%8A%A4" target="_blank" title="전설적인 프로그래머">도널드 커누스</a>)
```
시작하기 앞서 태그는 시작을 의미하는 `<>`와 닫힘을 의미하는 `</>`로 이루어져있다.  
태그를 사용하지 않을 경우에 HTML은 개행과 줄바꿈을 인식하지 않기 때문에  
모두 한 문장으로 이루어진 결과물을 보게 된다.

각 태그 이름별로 고유한 기능이 담겨있는데  주어진 예시 코드에서 `<a>` 태그는 링크를 의미하며,  
`h1`은 제목을 의미하는 태그, `string`은 강조를 의미하는 태그이다.

속성은 태그에 사용되어 추가적인 정보를 의미한다.  
예로 `<a>` 태그를 작성하여 링크라는 의미를 부여했지만 어떤 링크인지에 대한 정보가 없다  
따라서 속성을 부여함으로써 해당 링크가 어느 주소와 연결되어 있는지와 같은 정보를 담아낸다  
속성 이름별로 기능이 존재하기 여러 속성을 알아둘 필요가 있고, 딱히 순서는 상관하지 않는다.

정리하자면 `a` 는 태그이며 `href`는 `a` 태그의 속성이고,  `https://~`는 속성값이 되겠다.  
따라서 "도널드 커누스" 라는 글자를 눌렀을 때 해당 주소로 이동이 가능해지는 것이다.  
추가적으로 `target` 속성은 실행 대상을 지정하는데, `_blank`를 지정함으로써  
링크를 눌렀을 때 새 창에서 해당 주소로 이동을 하도록 한다.

<br><br>

### b.html

```html
<ol>
  <li>기술소개</li>
  <li>기본문볍</li>
  <li>하이퍼텍스트와 속성</li>
  <li>리스트와 태그의 중첩</li>
</ol>
<ul>
  <li>짱구</li>
  <li>철수</li>
  <li>맹구</li>
  <li>유리</li>
</ul>
```

`<li`> 태그를 이용하여 리스트를 만들 수 있지만,  
두 개 이상의 리스트를 생성하게 되면 구분을 할 수 없다.  
그리하여 순서를 부여하는 리스트 태그인 `<ol>` 태그, 그렇지 않은 태그인 `<ul>` 태그를 이용한다면  
리스트 별로 그룹화하여 나타낼 수 있고 이를 통해 태그는 중첩된다는 점을 알 수 있다.   
\* ol: Ordered List, ul: Unorderd List, href: Hypertext REFference

<br><br>

### index.html

```html
<!DOCTYPE html>
<html>
    <head>
    	<title>이것은 탭의 제목</title>
	    <meta charset="utf-8">
	</head>
  
	<body>
		<h1><a href="index.html">나는 큰 제목이다</a></h1>
		<ol>
			<li><a href="1.html">기술소개</a></li>
			<li><a href="2.html">기본문법</a></li>
			<li><a href="3.html">하이퍼텍스트와 속성</a></li>
			<li><a href="4.html">리스트와 태그의 중첩</a></li>
		</ol>
    
		<h1>나는 작은 제목이다</h1>
		대충 내용이라는 내용
    
		<h1>나는 작은 제목이다2</h1>
	</body>
</html>
```

`<!DOCTYPE HTML>`은 Document type declaration의 약자로 작성한 HTML의 표준을 지정한다.  
HTML에 존재하는 태그 기능들이 추가되거나 수정되어지고 있기에 버전별로 명시할 필요가 있다. 

`head`와 `body` 라는 태그가 존재하며 두 태그는 `html` 태그 안에 속해야 한다.  
또한 `head` 태그와 `body` 태그에서 사용하는 태그가 다르기 떄문에 알아두어야 한다.  
제목을 지정하는 `title`과 문서 기본 정보를 지정하는 `meta` 같은 태그는 `head` 태그에 포함되어야 하며,  그 외 `body` 태그에서 사용해야할 태그는 `body` 태그에서 사용되어야 한다.

`meta`에 `utf-8` 값을 지정해두면 한글이 안깨진다.

<br><br>

## 의미론적인 HTML

글에 요점이 존재하듯 코드에도 요점이 존재해야 한다.

```html
<!DOCTYPE html>
<html>
<head>
 	<meta charset="utf-8">
</head>
<body>
	<header>
		<h1><a href="http://localhost/">메인 컨텐츠</a></h1>
	</header>
	<nav>
		<ol>
			<li><a href="http://localhost/a.html">첫번째</a></li>
			<li><a href="http://localhost/b.html">두번째</a></li>
			<li><a href="http://localhost/c.html">세번째</a></li>              
		</ol>
	</nav>
</body>
</html>
```

앞서 배운 태그 이외에 `header`와 `nav` 라는 태그가 사용되었는데 해당 태그를 제외하고 작성을 하여도 결과물은 똑같다. 그럼에도 해당 태그를 사용하는 것이 바람직한 이유는 코드를 보았을 때 해당 페이지의 코드가 전달하려는 정보 의도를 전달할 수 있기 때문이다. HTML의 표준은 날이 갈수록 의미를 더 잘 전달할 수 있는 방향으로 나아가고 있기에 사용자들도 이에 익숙해져야겠다.



<br><br><br>

-----



# > 리눅스에 웹서버 구축하기

예제를 통해 학습을 하다보니 실제 내 서버에서 구동되는 모습이 보고 싶어졌다.  
그리하여 약간 순서를 바꾸어 VM Ware에 구축된 Ubuntu 20.04 환경에 구축할 예정이다.

## Bitnami: LAMP  

웹 구축에 필요한 `Apache`, `Mysql`, `Php`가 한번에 설치되는 Bitnami를 이용했다.  
`Apache`는 웹 서버요청 처리를 담당하여 html의 위치와 같은 정보를 로드할 수 있도록 돕는다.   
`MySQL`은 관계형 데이터베이스 관리 시스템으로 관계지향적이며,   
SQL언어를 이용하여 데이터를 관리할 수 있다.  
`PHP`는 페이지의 동적인 기능을 수행할 수 있도록 지원하는 오픈 소스 스크립트 언어이다.  

<br>


![image-20220418214853496](../img/image-20220418214853496.png)



<br><br><br>

------

#  > 한번 알아봤습니다.

- HTML의 약자에서 M은 Markup이라는 의미였는데 내 블로그는 Markdown 형식을 사용하고 있다. 
  그러면 이 둘의 차이는 무엇이지?

> 검색을 하던 중 알게된 점은 Markup은 프로그래밍과는 구별된 언어라는 것이다.  
> 단순히 데이터를 기술하는 정도로만 사용되기 때문이라고 한다.  
> 아마 다른 언어와 달리 가공을 하지 않아서 인것 같다.
>
> Markdown은 Markup의 일종으로 단순한 사용방법이 적용된 언어이다.  
> HTML처럼 많은 태그를 작성하지 않아도 편하게 데이터를 기술할 수 있다.  
> 확실히 줄바꿈을 스페이스 2번과 개행이면 구분된다는 점이 편리한 것 같다.





