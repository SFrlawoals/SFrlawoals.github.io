---
layout: default
title: Day23
nav_order: 2

parent: Hacking Project - W4
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > http://suninatas.com/

자취방 입주 전까지 시간이 생겨서 뒤에 문제들도 풀어보았다.  

## challenge 5

![image-20220509181908772](../img/image-20220509181908772.png)

페이지 소스를 보고 순간 흠칫했다.  
응애 시절 웹 소스 코드 내부에 알 수 없는 자바스크립트가 있으면 일단 닫기 버튼을 눌렀었는데,  
지금은 이 친구가 어떤 동작을 수행하겠구나 하며 이해는 안되지만 나가지는 않았다.

힌트에서는 요상한 숫자를 제출하도록 했으나 아무 반응이 없었고,  
저 자바스크립트를 이해해야 한다는 목표를 잡고 삽질을 했다.

![image-20220509182200036](../img/image-20220509182200036.png)

정말 순수하게도 가지런히 코드를 정리해봤는데 도통 이게 무슨 코드인지 몰랐다.  
내가 자바스크립트를 한참 모르는 것인가 하고 생각을 해봤는데,  
아니 어떤 코드가 저런 식으로 적혀서 동작을 하냐는 의구심이 생겼다.

또 어디서 줏어들은 난독화? 암호화? 같은 것이 아닐까 검색을 해보았고,  
평문으로 변환 시켜주는 사이트를 찾아 적용해보았더니 재미있게도

```javascript
var digitArray = new Array('0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f');
function PASS(n) {
	var result = '';
	var start = true;
	for (var i = 32; i > 0;) {
		i -= 4;
		var digit = (n >> i) & 0xf;
		if (!start || digit != 0) {
			start = false;
			result += digitArray[digit]
		}
	}
	return (result == '' ? '0': result)
}
```

진짜 내가 이해할 수 있는 자바스크립트가 나왔다.  
전체적인 흐름으로 보면 어떤 작업이 이루어지고 그 결과가 NULL이 아닐 경우,  
그에 대한 결과 값을 출력해주는 것 같다고 판단이 되었다.

그리하여 개발자 도구의 콘솔창에서 냅다 힌트와 PASS()를 결합했더니

![image-20220509182617598](../img/image-20220509182617598.png)

뭔가 튀어나왔고 이를 제출란에 입력했더니 flag가 나왔다. 진짜 띠용이였다.

<br>

## challenge 6

일단 게시글을 읽는 것 자체는 어렵지 않았다.

![image-20220509183601324](../img/image-20220509183601324.png)

3번째 게시글을 클릭하면 사용자의 쿼리가 어떤 형태로 전송되는지 나와있고,  
SQL Injection에 대해 맛만 봤더라면 예측할 수 있는 `' or 1=1`을 대입했다.

그리고 No! Hacking! 이라는 문구를 보았고, select 와 `=` 기호 등이 포함되면 전부 실패했다.  
그렇게 필터링 되어 있다는 점을 확인하고 어떻게든 참을 만들기 위하여 다양한 시도를 한 결과  
`>` 기호가 필터링 되지 않는다는 점을 보고 게시글 확인까지 넘어갔다.

![image-20220509190746492](../img/image-20220509190746492.png)

넘어온 사이트에는 정말 볼게 없었는데 웹 소스에서 키에 대한 힌트를 줬다.  
해당 사이트에 넘어올 때 `auth_key`는 suninatastopofworld!라고 해서 이게 flag인줄 알았으나,  
인증이 되질 않았고 그 외 게시글에 https://md5hashing.net/ 를 참고하라 하여 세션 값도 복호화해봤다.

그러나 의미없는 삽질이었고 생각보다 찾기 어려웠다.  
근데 뜬금없이 존재하는 저 `KEY_HINT`의 value를 검색해보았더니

![image-20220509191027560](../img/image-20220509191027560.png)

한번 쯤은 들어봤을 법한 왕의 이름이 나왔다.  
그리고 그냥 인증해봤는데 이게 flag값이였다.  
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ하

<br>

## challenge 7

들어가자마자 아이유 사진이 보여서 아이유 노래를 바로 틀었다.  
이쁜 사진과는 다르게 더러운 코드가 있었는데 아래와 같았다.

```html

<!DOCTYPE html>

<head>
    <title>Game 07</title>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <link rel="shortcut icon" href="/static/img/game.ico" />
    <script>
        function noEvent() {
            if (event.keyCode == 116 || event.keyCode == 9) {
                alert('No!');
                return false;
            }
            else if (event.ctrlKey && (event.keyCode = 78 || event.keyCode == 82)) {
                return false;
            }
        }
        document.onkeydown = noEvent;
    </script>
</head>
<body onload="javascript:document.frn.wen07.focus();">
    <form method="get" name="frn">
        <div align="center">
            <input type="text" name="wen07" value="Do U Like girls?">
        </div>
    </form>
    <form method="post" action="./web07_1.asp" name="frm">
        <div align="center">
            <input type="button" name="main_btn" value="main" style="width: 60" onclick="location.href = '/'">&nbsp&nbsp&nbsp
            <input type="button" name="main_btn" value="Back" style="width: 60" onclick="history.back()">
        </div>
        <div align="center">
            <input type="hidden" name="web07" value="Do U Like girls?">
        </div>
        <div align="center">
            <img src="./iu.gif" width="700" height="1000" name="iu">
        </div>
        <br>...<br>
        <div align="center">
            <input type="submit" value="YES">
        </div>
        <br>...<br>
        <div align="center">
            <img src="./yoona.gif" alt="yoona" width="700" height="1000">
        </div>
    </form>
</body>

<!-- Hint : Faster and Faster -->
<!-- M@de by 2theT0P -->

```

사진들 사이에는 아래 버튼이 하나 있었는데

 ![image-20220509191626994](../img/image-20220509191626994.png)

[사진1] ------------------------ [버튼] ------------------------ [사진 2]

이런 식 이였다.  
그리고 해당 버튼을 누르면 너는 늦었다고 한다.  
아마도 빠르게 누르라는 의미인 것 같은데 위 코드에서 특정 키를 제한한다

116 과 9, ctrl+(78, 82)를 제외했는데 알아볼 수 있도록 찾아보니  
f5와 tab, ctrl+(n,r) 이였고 전부 페이지 새로 고침과 관련되었다.  
무한 트라이가 불가능하다는 것을 의미하는 것일까?

그 외에 또 수상한 점이 하나 있었는데

```html
<form method="get" name="frn">
	<input type="text" name="wen07" value="Do U Like girls?">
...
<form method="post" action="./web07_1.asp" name="frm">
	<input type="hidden" name="web07" value="Do U Like girls?">
```

form이 2개라는 점과 wen vs web, frn vs frm, hidden을 통한 데이터 전달이 있다는 점이었다.  
이것을 어떻게 쓸지 고민을 해봐야 한다.

web07_1.asp에서 무언가 수행한다고 예상하기에 일단 무작정 콘솔창에서 `frm.submit()`을 이용하여 제출했더니... 풀렸다... 음?

