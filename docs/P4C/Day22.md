---
layout: default
title: Day22
nav_order: 1

parent: Hacking Project - W4
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 점프 투 파이썬과 공부하기



파이썬 사용법을 어느 정도 알고 있기에 새로 알게 된 사실이나 기록해두면 유용할 것 같은 정보를 모아보았다.

<br>

## if __name__ == "__main__": 의 의미

옛날에는 그저 해당 조건문 아래부터가 c언어의 `main()`처럼 사용되는 줄 알았는데, 모듈이 참조되어 질 때 해당 모듈이 작업되는 파일이 아님을 인식해주는 역할이었다. 참조되는 모듈에서 `__name__` 변수의 값을 확인해보면 파일명인 것을 확인할 수 있다. 반대로 참조하는 파일에서 `__name__` 변수의 값은 `__main__`이기에 내부 코드가 실행되는 것이다.

<br>

## 다른 파일에서 모듈 불러오기

파이썬에서는 `import`를 통해 다른 .py 파일의 함수를 사용할 수 있다. 이 때 참조하는 파일이 작업 중인 파일과 경로가 일치해야 했는데, 다른 방법으로는 PYTHONPATH 환경 변수를 사용하는 방법이 있다.

```python
C:\doit>set PYTHONPATH=C:\doit\mymod
```

<br>

## __init__.py 의 용도

`__init__.py` 파일은 해당 디렉터리가 패키지의 일부임을 알려주는 역할을 한다. 패키지에 포함된 디렉터리에 `__init__.py` 파일이 없다면 패키지로 인식되지 않는다.

특정 디렉터리의 모듈을 `*`를 사용하여 import할 때에는 다음과 같이 해당 디렉터리의 `__init__.py` 파일에 `__all__` 변수를 설정하고 import할 수 있는 모듈을 정의해 주어야 한다.  `__all__`이 의미하는 것은 참조한 모듈의 디렉터리에서 * 기호를 사용하여 import할 경우 이곳에서 정의한 특정 모듈을 import한다는 의미이다.

<br>

## try .. finally

`try_except`를 이용해 예외처리를 하는 방법도 있지만, try문에는 finally절을 사용할 수 있다. finally절은 try문 수행 도중 예외 발생 여부에 상관없이 항상 수행된다. 보통 finally절은 사용한 리소스를 close해야 할 때에 많이 사용한다.

<br>

## 오류 회피하기

```python
try:
    f = open("나없는파일", 'r')
except FileNotFoundError:
    pass
```
오류를 그냥 회피한다.

<br>

## 오류 일부러 발생시키기

```python
def fly(self):
    raise NotImplementedError
```
오류를 강제로 발생시킨다.

<br>

## enumerate

enumerate는 "열거하다"라는 뜻이다. 이 함수는 순서가 있는 자료형(리스트, 튜플, 문자열)을 입력으
로 받아 인덱스 값을 포함하는 enumerate 객체를 돌려준다.

```python
>>> for i, name in enumerate(['body', 'foo', 'bar']):
...     print(i, name)
...
0 body
1 foo
2 bar
```

<br>

## filter

filter 함수는 첫 번째 인수로 함수 이름을, 두 번째 인수로 그 함수에 차례로 들어갈 반복 가능한 자료형을 받는다.

```python
#positive.py 
def positive(l): 
    result = [] 
    for i in l: 
        if i > 0: 
            result.append(i) 
    return result

print(positive([1,-3,2,0,-5,6]))
```
위 코드는 아래 두 코드와 동일한 기능을 한다.

<br>
```python
#filter1.py
def positive(x):
    return x > 0

print(list(filter(positive, [1, -3, 2, 0, -5, 6])))
```
```python
print(list(filter(lambda x: x > 0, [1, -3, 2, 0, -5, 6])))
```

<br>

## map

map(f, iterable)은 함수(f)와 반복 가능한(iterable) 자료형을 입력으로 받는다.

```python
# two_times.py
def two_times(numberList):
    result = [ ]
    for number in numberList:
        result.append(number*2)
    return result

result = two_times([1, 2, 3, 4])
print(result)
```
위 코드는 아래 코드와 동일한 기능을 한다.

<br>
```python
print(list(map(lambda a: a*2, [1, 2, 3, 4])))
```

<br>

## zip

동일한 인덱스에 있는 데이터끼리 묶어 주는 역할을 하는 함수이다.
```python
>>> list(zip([1, 2, 3], [4, 5, 6]))
[(1, 4), (2, 5), (3, 6)]
>>> list(zip([1, 2, 3], [4, 5, 6], [7, 8, 9]))
[(1, 4, 7), (2, 5, 8), (3, 6, 9)]
>>> list(zip("abc", "def"))
[('a', 'd'), ('b', 'e'), ('c', 'f')]
```



<br><br><br>

# http://suninatas.com/

## challenge 1

![image-20220509134451673](../img/image-20220509134451673.png)

단순히 python 사용법을 익히기 위한 문제였다.
그래서 **ami**를 입력하여 키 값을 얻었다.

<br>

## challenge 2

![image-20220509150434736](../img/image-20220509150434736.png)

처음에 로그인 창 밖에 없어서 개발자 도구를 이용하여 코드를 보았다.  
스크립트가 포함되어 있었는데 요구사항은 모순적인 상황을 요구했다.

아이디와 비밀번호가 일치하도록 하면 flag를 준다는 힌트가 있었지만  
아이디와 비밀번호가 일치하면 얻을 수 없다는 코드가 존재했다.

그리하여 HTTP 통신을 하기 이전을 우회하기 위해서는 아이디와 비밀번호를 다르게하고,  
실질적인 통신 때에는 아이디와 비밀번호가 동일하도록 해야겠다.  
그러기 위해서는 burp suite를 이용하여 질의 패킷을 수정하기로 했다.

프록시를 이용하여 전달하기 전 POST 값을 확인하였고
![image-20220509151106551](../img/image-20220509151106551.png)
{: .center}

<br>

수정하여 통신한 결과이다.
![image-20220509151055542](../img/image-20220509151055542.png)

<br>

## challenge 3

![image-20220509151925889](../img/image-20220509151925889.png)
문제의 사이트 자체에는 주어진 글자 외에는 단서가 없었다.  
그래서 NOTICE 보드에 글을 쓰러 갔는데

![image-20220509152012367](../img/image-20220509152012367.png)
여타 다른 게시판들처럼 글쓰기 버튼이 존재하지 않았다.  
그래서 이전에 내가 웹사이트를 만들었던 과정을 생각해보다가  
글쓰기 버튼을 추가하여 `write_action.php`로 수행하던 기억이 났다.

<br>

그리하여 다른 게시판에서 글을 쓸 때 어떤 수행을 통하는지 확인해보니

![image-20220509152146690](../img/image-20220509152146690.png)

URL에서 write라는 경로가 추가됨을 확인했다.  
그리하여 NOTICE 게시판의 경로에서 `/write`를 추가했더니 풀렸다

![image-20220509152238801](../img/image-20220509152238801.png)

아무 글이나 작성하니 flag를 줬다.

<br>

## challenge 4

![image-20220509152927166](../img/image-20220509152927166.png)

처음 접속하였을 때 모습과 소스 코드이다.  
아마도 수행하는 `web04_ck.jsp` 내부에 무언가 존재하는 듯하다.



![image-20220509152604996](../img/image-20220509152604996.png)

25point 가 넘어가니 해당 팝업이 뜨면서 더 이상 진행되지 않았다.  
그리하여 user_agent의 값을 burp suite를 이용해 변경했더니...

![image-20220509153923328](../img/image-20220509153923328.png)



아래와 같이 26점까지 올라갔다.  
그리하여 성공한 쿼리를 burp suite의 `repeater` 기능을 이용해 25번 진행했다.

![image-20220509155119652](../img/image-20220509155119652.png)

그리고 정확히 50점이 되자 flag값을 얻었다.



<br><br>

혼자 고민하고 풀어나가는 게 재밌어서 하루 만에 다 해버렸다.  
4번 문제를 풀면서 매번 패킷을 수정하는 것이 귀찮았는데 그 덕분에 `repeater` 기능을 알 수 있었다.  
시간이 많이 남았으니 뒷 문제들도 해봐야겠다.

![image-20220509155218345](../img/image-20220509155218345.png)
