---
layout: default
title: Day62
nav_order: 3

parent: Hacking Project - W9
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

오늘 면접봤는데 이번 11기는 무조건 떨어진 것 같다 ㅋㅋㅋㅋㅋㅋㅋㅋㅋ   
진짜 진짜 아쉽긴 한데 좋은 경험이 였다고 생각하고 다음 지원을 위해서 열심히 해야겠다.  
한 문제라고 풀고 자야 내일의 내가 무기력한 상태로 있지 않을 거라고 믿는다.

# > Webhacking.kr:old-14(js)

## writeup

```javascript
function ck(){
  var ul=document.URL;
  ul=ul.indexOf(".kr");
  ul=ul*30;
  if(ul==pw.input_pwd.value) { location.href="?"+ul*pw.input_pwd.value; }
  else { alert("Wrong"); }
}
```

자바스크립트에서 처음 보는 함수가 나왔다. `indexOf` 라는 함수는 문자열 뒤에 `.` 기호를 붙여 사용하며, 해당 문자열이 몇 번째 위치에 존재하는지를 반환한다고 한다. 그래서 현재 주소를 적어보면

`https://webhacking.kr/challenge/js-1/` 이고 여기서 ".kr" 문자의 위치는 18번째 이후이다.  그래서 18\*30dls 540을 넣었더니 풀렸다.

![image-20220618211226900](../img/image-20220618211226900.png)

<br><br><br>

-----

# > Webhacking.kr:old-15(js)

## writeup

![image-20220618220232817](../img/image-20220618220232817.png)

이 문제에 들어가는 순간 위와 같은 팝업창 이후에 메인 페이지로 돌아간다. 그래서 이 문제는 현재 막혀있는 줄 알았는데 팝업창 내용이 "접근 거부" 이다. 

팝업창은 자바스크립트로 띄우기에 게싱해보면 `alert`호출로 웹페이지에 적힌 플래그를 못읽도록 하는게 아닐까?

```html
<html>
<head>
<title>Challenge 15</title>
</head>
<body>
<script>
  alert("Access_Denied");
  location.href='/';
  document.write("<a href=?getFlag>[Get Flag]</a>");
</script>
</body>
```
예상했던대로 그랬고

![image-20220618230536309](../img/image-20220618230536309.png)

개발자 도구로 자바스크립트를 껐다. 위 링크는 비록 안나왔지만 주소에 `?getFlag`를 붙혀서 풀었다.


-----

# > Webhacking.kr:old-16(js)

## writeup

```html
<html>
<head>
<title>Challenge 16</title>
<body bgcolor=black onload=kk(1,1) onkeypress=mv(event.keyCode)>
<font color=silver id=c></font>
<font color=yellow size=100 style=position:relative id=star>*</font>
<script> 
document.body.innerHTML+="<font color=yellow id=aa style=position:relative;left:0;top:0>*</font>";
function mv(cd){
  kk(star.style.left-50,star.style.top-50);
  if(cd==100) star.style.left=parseInt(star.style.left+0,10)+50+"px";
  if(cd==97) star.style.left=parseInt(star.style.left+0,10)-50+"px";
  if(cd==119) star.style.top=parseInt(star.style.top+0,10)-50+"px";
  if(cd==115) star.style.top=parseInt(star.style.top+0,10)+50+"px";
  if(cd==124) location.href=String.fromCharCode(cd)+".php"; // do it!
}
function kk(x,y){
  rndc=Math.floor(Math.random()*9000000);
  document.body.innerHTML+="<font color=#"+rndc+" id=aa style=position:relative;left:"+x+";top:"+y+" onmouseover=this.innerHTML=''>*</font>";
}
</script>
</body>
</html>
```

`//do it` 이라는 주석을 보고 콘솔창에 `mv(124)` 를 했는데 풀렸다.

아니 이게 왜 풀린거지?

![image-20220618211510707](../img/image-20220618211510707.png)


-----

# > Webhacking.kr:old-17(js)

## writeup


```javascript
unlock=100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+1/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10+100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10-100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10/100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10*100*10*10+100/10-10+10+50-9*8+7-6+5-4*3-2*1*10+9999999;
function sub(){ if(login.pw.value==unlock){ location.href="?"+unlock/10; } else{ alert("Wrong"); } }
```

해당 문제에 포함되어 있는 스크립트이고 입력 란에 unlock값을 넣어주면 풀리는 듯하다. 콘솔값에 위 식들을 넣었고 나온 결과를 입력란에 넣었다.

![image-20220618231020869](../img/image-20220618231020869.png)

clear !!!

![image-20220618230937817](../img/image-20220618230937817.png)