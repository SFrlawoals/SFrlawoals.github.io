---
layout: default
title: Day9
nav_order: 2

parent: Develop Project - W2
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

어제 기록을 보니 내가 아닌 내가 있었던 것 같다.  
그래서 이왕 시작한 김에 페이지의 대문을 완전히 만들고 넘어갈 생각이다.

# > 생활 코딩과 공부하기

[https://opentutorials.org/course/2418/13339](https://opentutorials.org/course/2418/13339) CSS

<br>

## [+] 맛보기

학습을 하다 보니 그동안 css에 대해서 사용했던 용어들이 얼마나 무지했는지 알았고,  
잘 모르는 용어 사용에 주의해야 함을 깨달았다.

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      li{
        color:red;
        text-decoration:underline;
        font-size:20px;
      }
    </style>
  </head>
  <body>
    <ul>
      <li>HTML</li>
      <li>CSS</li>
      <li>JavaScript</li>
    </ul>
  </body>
</html>
```

`style` 태그는 이 안에 있는 css 문법을 html 적용하겠다는 의미이다.  
적용할 태그의 이름을 `style` 태그 안에 작성을 한 뒤에 `{}` 안에 효과들을 넣을 수 있다.  

위 코드를 예시로 용어에 대한 정리를 해보자면, `li`는 선택자(Selector)가 되는 것이고 `{}` 안에 있는 내용은 선언(declaration)이 된다. `color`는 속성, `red`는 속성 값, `:`는 속성과 속성값을 구분하는 구분자, `;`는 선언과 선언을 구분해주는 구분자가 된다.

`color`는 색상, `text-decoration`은 글자 형태, `font-size`는 글자 크기를 의미하여 이 외에 꾸미기 위한 속성 값은 필요에 따라 찾아보면서 진행하면 되겠다.

<br>

```html
<style>
	li{
        color:red;
        text-decoration:underline;
     }
     #select{
        font-size:50px;
     }
</style>
```

다음은 어제도 어렴풋이 익혔던 내용인데 선택자에 `#` 또는 `.` 기호를 붙히지 않을 경우에는 이를 **태그 선택자**라고 부르며 HTML에서 사용되는 모든 태그에 해당 선언이 적용된다.   
하지만 `#`을 붙힐 경우에는 **ID 선택자**라고 부르며 HTML의 `id` 속성에 해당 선택자가 적힌 태그에만 해당 선언이 적용된다. 그리고 하나의 태그에서만 사용해야하는 것이 약속이다.  
`.`을 붙힌 경우에는 **클래스 선택자**라고 부르며 HTML의 `class` 속성에 해당 선택자가 적힌 태그 모두에 선언이 적용된다.

ID 와 클래스 선택자가 구분되는 이유는 이름에서도 볼 수 있는데 ID는 개개인의 고유 정보를 의미하여 각각의 특성을 나타내기 위한 것이다. CLASS는 그룹 또는 반과 같은 의미로써 속해있는 객체들의 특성을 나타내는 역할을 한다. 따라서 사용할 때 이미 사용된 ID 선택자는 사용하지 않는 것이 올바르다.

<br>

```html
<style>
	ul li{
  	  color:red;
	}
	#test>li{
    	border:1px solid red;
	}
    ul,ol{
	    background-color: powderblue;
	}
</style>
```

선택자 생성 시 부모 자식 선택자를 생성할 수 있다.  
`ul li`로 선택자를 작성하면 `ul` 태그 아래에 있는 모든 `li` 태그에 선언이 적용된다.  
`#test > li`로 작성하면 id값이 `#test`인 태그 바로 아래에 있는 `li` 태그에만 선언이 적용된다.  
`ul,ol` 은 `ul`태그와 `ol` 태그 둘 다 선언이 적용이 된다.

선택자에 관해서 정말 재밌는 공부 사이트가 있었는데 [https://flukeout.github.io/#](https://flukeout.github.io/#) 이다.  
선택자에 `plate.apple`를 작성하면  `plate` 태그의 `apple` 클래스를 의미힌다.  
`*`을 입력하면 모든 태그에 적용된다.  
`plate *`을 입력하면 `plate` 안에 있는 모든 태그가 적용된다.  
`plate + apple`을 입력하면 `plate` 다음에 오는 모든 `apple` 태그가 적용된다.  
`plate + pickle` 을 입력하면 `plate` 다음, `pickle` 포함 이전의 모든 태그가 적용된다.  
`orange :first-child`을 입력하면 `orange` 태그의 가장 첫번째 태그만 적용된다.  
`~: only-child`는 해당 태그에 자식 태그가 하나인 태그에만 적용되고,  
`~: last-child`는 해당 태그에서 마지막 태그에만 적용된다.

<br>

선택자 중에서 가상 클래스 선택자라는 것이 존재하는데 유용한 것들을 적어보았다.

```html
<style>
    a:link{
    	color:red
    }
    a:visited{
        color:purple
    }
    a:hover{
        color:yellow
    }
    a:active{
        color:green
    }
    a:focus{
        color:white
    }
</style>
```

`link`는 방문한 적이 없는 링크, `visited`는 방문한 적이 있는 링크, `hover`는 마우스를 올렸을 때,  
`active`는 마우스를 클릭했을 때, `focus`는 tab 키와 같은 것으로 선택되었을 때 적용된다.

<br>

Cascading Style Sheet에서 Cascading은 "폭포", 즉 아래로 흐르는 규칙이 존재한다.  
따라서 우선순위에 대한 개념을 알고 있어야 페이지를 단장할 때 도움이 된다.

```html
<style>
	li{color:red;}
	#idsel{color:blue;}
	#idsel{color:yellow;}
	.classsel{color:green;}
</style>
<body>
    <ul>
      <li>html</li>
      <li id="idsel" class="classsel">css</li>
      <li>javascript</li>
    </ul>
</body>
```

위와 같은 코드를 작성하였을 때 "css" 라는 단어는 `color:yellow`가 적용된다.  
그 이유는 ID 선택자 선언 시 가장 마지막 선언이 적용되며,  
클래스 선언자보다 ID 선언자의 우선순위가 더 높기 때문이다.

```html
<li id="idsel" class="classsel" style="color:black">css</li>
```
이렇게 작성하게 되면 css 글자는 검은 색으로 바뀌게 될 것이다.  
그리하여 정리해보면 다음과 같다.

**!important > style attribute > id selector > class selector > tag selector**

`!important`는 우선순위를 강제하기 위하여 속성 값 옆에 삽입하는 것으로,  
그 이외에는 구체적이고 명시적인 속성 일수록 우선 순위가 더 높다는 것을 알 수 있다.



## [+] 레이아웃, 이제 시작

로그인 폼의 전체적인 틀을 잡아둔 만큼 그것을 이쁘게 배치하는 것이 목표인데,  
인라인(Inline)과 블럭(Block)레벨의 수준을 이해하며 시작하겠다.

<div class="code-example">
<h1>hello world</h1>
이것은 <a href="example.com">test</a> 링크입니다.
</div>

```html
<h1>hello world</h1>
이것은 <a href="example.com">test</a> 링크입니다.
```
이 코드를 이용하여 살펴보면 여태껏 아무 의심없이 넘어갔던 행간에 대한 의문이 든다.  
`h1` 태그를 이용하였을 때에는 문장의 끝에 개행이 삽입되었던 반면,
`a` 태그를 이용했을 때에는 다음 문장과 자연스럽게 이어지는 것을 볼 수 있다.

그리고 그 차이는 화면 전체를 사용하는 태그인 **블럭**과 화면의 일부만을 차지하는 태그인 **인라인**이라고 설명된다. 즉 태그별로 이러한 특성이 존재하기에 위와 같은 결과를 볼 수 있는 것이다. 이러한 속성을 임의적으로 넣을 때에는 다음과 같다
```html
<style>
h1{display: inline;}
a{display: block;}
</style>
```
이렇게 되면 추후 `h1` 태그에 감싸진 텍스트 뒤에는 개행이 붙지 않고, 
`a` 태그에 감싸진 텍스트는 다음 텍스트 전에 줄바꿈이 발생하게 될 것이다.

<br>

중요한 박스모델에 관한 내용으로 가장 많이 사용되는 css top10의 70%가 아래 속성을 사용한다. 

```html
<style>
	p{
		border-width: 10px;
		border-style: solid;
		border-color: red;
		padding: 20px;
		margin: 30px;
		width: 120px;
    }
    h1{
		border: 20px solid blue;
		height: 40px;
    }
</style>
```
`border` 속성을 통해 테두리의 너비와 스타일, 색을 지정할 수 있는데  
`h1`에서 사용된 속성처럼 한 줄로도 표현이 가능하다.  
`padding` 속성은 컨텐츠와 박스 사이의 간격을 의미한다.  
`margin`은 박스와 박스 사이의 간격을 의미한다.  
`width` 는 박스의 너비를 의미하는데 보통 `height`는 잘 쓰지 않는다.

또한 해당 박스 속성은 `inline` 레벨보다는 `block` 레벨에서 많이 사용된다.  
`a` 태그와 같은 곳에서는 width 속성이 적용되지 않는다는 점을 알아두자.

참 보잘 것없는 그림이지만 한 눈에 이해하기 위한 그림이다 ㅠ.

![image-20220426163733501](../img/image-20220426163733501.png)

<br>

`border`를 이용하여 테두리를 지정하다 보면 박스의 `width`의 속성값이 `border-width`의 속성값으로 인해 실질적으로 계산하기 어려울 수가 있다. 그 이유가 `width`의 기준이 컨텐츠 자체의 너비이기 때문이다. 그러한 경우에는 `box-sizing` 이라는 속성을 이용하여 박스의 `width`가 `border-width`의 영향을 받도록 설정하면 좀 더 수월하게 작업할 수 있다.
```html
<style>
	*{ box-sizing: border-box; /*default:content-box*/ }
</style>
```

<br>

마진 겹침은 이러한 것이 있구나 하는 정도만 짚자면 다음과 같다.
```html
<style>
        #parent{
			border:1px solid red;
            margin-top:100px;
            margin-bottom:70px;
        }
        #child{
            background-color: powderblue;
            margin-top:50px;
        }
</style>
```
`parent` 선택자를 부모로 둔 태그가 `child` 선택자를 적용했다고 가정하면,  
실질적으로 두 태그 사이의 margin값은 70px이다. 이것이 마진 겹침이다.  
이 외에 다양한 겹침 사례가 있는데 이는 실제 디자인 시에 찾아봐야 겠다.

<br>
`position`이라는 속성도 존재하는데 해상 속성값에는 `static`과 `relative`, `absolute`, `fixed`가 존재한다. 기본 default 값이 `static`이며 위치가 고정적이다. `relative`는 부모 태그에 영향을 받아 위치가 정해지며 틀을 벗어날 수 없다. `absolute`는 틀을 벗어나 절대적인 수치를 이용할 수 있지만 어느 정도 한계가 있다. `fixed`는 어디든 위치할 수 있으며 절대적인 수치를 바탕으로 자리 잡는다.

누군가 비유하여 표현해준 댓글이 있어서 인용했다
> static 아이 /자아 생성전
relative 어린이 /부모에 영향받는
absolute 청소년 /자신의 적합한 롤 모델을 찾는
fixed 성인 /자신의 위치를 잡은

<br>

## [+] flex

레이아웃을 효과적으로 표현할 때 사용되는 도구로 생각보다 많은 css를 볼 때 해당 값을 볼 수 있었다. 그러나 어떤 의미를 갖는지를 모른 채 보고 있었는데 이번 기회에 감을 잡아보려 한다.

```html
<style>
	.container{
		display: flex;
	}
	.item{
	}
</style>
```

위 코드가 기본적으로 flex 를 사용하기 위한 형태이다.  
`container`와 `item` 이라는 선택자 이름을 반드시 써야하는 것은 아니지만,  
부모가 될 태그에 `display` 속성에 `flex`라는 속성 값을 반드시 선언해야 한다.

아래는 flex에서 사용되는 속성인데 간단히 보면

```html
flex-direction:row;
flex-basis: 200px;
flex-grow: 0;
flex-shrink: 0;
```

`flex-direction`은 태그에 포함된 컨텐츠들의 정렬 방식을 나타내며 row, column, row-reverse, column-reverse 등이 가능하다.   
`flex-basis`는 정렬된 형태를 기준으로 너비를 지정할 수 있다.   
`flex-grow`는 값이 1보다 클 경우 해당 값에 비례하여 일정한 너비를 갖게 된다.   
`flex-shrink`는 값이 0으로 설정이 될 경우 웹 페이지 자체가 줄어들어도 컨텐츠 크기는 줄어들지 않는다.  
모두 글로는 이해하기 어려우나 어떻게 동작하는지 보면 바로 감이 온다. 영상이 최고인 것 같다.

<br><br><br>

-----

# > 레이아웃만 잡아보기

이론만 듣다 보니 많이 지루했기에 직접 만들어보며 모르는 것들을 찾기로 했다.  
이제는 다른 css 코드를 보더라도 어떤 의미인지 감이 오기에 내 것으로 만들기로 했다.

...를 하려했는데 생각보다 만드는게 어렵다. 내일 꼭 만들어야겠다.
