---
layout: default
title: Hacking Project - W5
nav_order: 5

has_children: true
parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# W5: DVWA 환경 구축 및 실습 진행

<br>

- Low 레벨 올클 및 write-up 작성
- DVWA에 제공되는 취약점에 대해 최대한 확실히 이해하기(완벽하게 이해할 필요는 없음)
- 공개된 라업을 참고해도 됨 but 최대한 확실히 이해하기

<br>
<br>
<br>

------

# 정리글



## > 환경 구축 과정

[https://jminis.github.io/docs/P4C/Day29/#-dvwa-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0](https://jminis.github.io/docs/P4C/Day29/#-dvwa-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0)

<br>

## > Low 문제별 라업

1. [Brute Force](https://jminis.github.io/docs/P4C/Day29/#-dvwa-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0)
2. [Command Injection](https://jminis.github.io/docs/P4C/Day32/#-dvwa-command-injection)
3. [CSRF](https://jminis.github.io/docs/P4C/Day32/#-dvwa-cross-site-request-forgery-csrf)
4. [File Inclusion](https://jminis.github.io/docs/P4C/Day32/#-dvwa-file-inclusion)
5. [File Upload](https://jminis.github.io/docs/P4C/Day34/#-dvwa-file-upload)
6. [Insecure CAPTCHA](https://jminis.github.io/docs/P4C/Day34/#-dvwa-insecure-recaptcha)
7. [SQL Injecition](https://jminis.github.io/docs/P4C/Day34/#-dvwa-sql-injection)
8. [SQL Injecition (blind)](https://jminis.github.io/docs/P4C/Day34/#-dvwa-sql-injection-blind)
9. [Weak Session IDs](https://jminis.github.io/docs/P4C/Day34/#-dvwa-weak-session-ids)
10. [XSS (DOM)](https://jminis.github.io/docs/P4C/Day34/#-dvwa-dom-based-xss)
11. [XSS (Reflected)](https://jminis.github.io/docs/P4C/Day34/#-dvwa-reflected-xss)
12. [XSS (Stored)](https://jminis.github.io/docs/P4C/Day35/#-dvwa-xss-stored)
13. [CSP Bypass](https://jminis.github.io/docs/P4C/Day35/#-dvwa-csp-bypass)
14. [JavaScript](https://jminis.github.io/docs/P4C/Day35/#-dvwa-javascript-attacks)

<br>

## > low 끝

flag 인증 방식이 아닌 실제 구축된 환경에서 테스트 방식으로 학습이 익숙하지 않았지만, 오히려 더 다양하게 시도해볼 수 있어서 좋았던 것 같습니다. 생각 그대로를 글로 옮기려다보니 난잡한 라업이 되었지만 훗날 완성을 위한 부품들을 다 꺼내어 살펴보았다고 생각합니다. 다음 단계 문제도 열심히 풀겠습니다.

**다음 주 일정은 mysqld 자격증 시험, bob 서류 접수**