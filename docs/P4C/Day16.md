---
layout: default
title: Day16
nav_order: 2

parent: Develop Project - W3
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 서버 호스팅하기

기존에 계획했던 대로 아마존 EC2 인스턴스를 이용하여 서버를 운영할 예정이다.  
운영체제는 `Ubuntu 18.04`를 사용하였고, 인스턴스 유형은 무료인 `t2.micro`이다.

인스턴스 시작 이후 가장 먼저 진행한 것은 EC2에도 Bitnami를 설치하였다.  
사용한 명령어 순서는 다음과 같다.  

```bash
$ wget https://bitnami.com/redirect/to/2007913/bitnami-lampstack-8.1.4-0-linux-x64-installer.run
$ chmod 700 bitnami-lampstack-8.1.4-0-linux-x64-installer.run 
$ sudo ./bitnami-lampstack-8.1.4-0-linux-x64-installer.run 
$ sudo apt install libncurses5
```
이후 로컬에서 사용했던 설정들을 서버에서도 적용시켰다.

1.기본페이지 설정
```bash
$ sudo vi /opt/lampstack-8.1.4-0/apache2/conf/httpd.conf
```
<div class="code-example">
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>
</div>

<br>

2.mysql 설정
```bash
$ export PATH=$PATH:/opt/lampstack-8.1.4-0/mariadb/bin
$ mysql -u root
```
bitnami에 설치된 Mysql 실행을 위해 PATH를 등록해주고 mysql을 실행한다.

```sql
MariaDB [mysql]> USE mysql;
MariaDB [mysql]> SET password FOR 'root'@'localhost' = PASSWORD('password');
```
보안을 위해 root 비밀번호를 설정한다.

```bash
$ sudo /opt/lampstack-8.1.4-0/ctlscript.sh restart
```
변경한 설정을 적용시키기 위해 재시작한다.

<br>

3.시간 설정
```bash
$ sudo dpkg-reconfigure tzdata
```
Asia -> Seoul 
{: .code-example}
시스템 시간을 먼저 한국으로 맞춰주고

```bash
$ sudo vi /opt/lampstack-8.1.4-0/php/etc/php.ini
```
date.timezone = Asia/Seoul
{: .code-example}
웹 서버 시간도 한국

```bash
$ sudo vi /opt/lampstack-8.1.4-0/mariadb/my.cnf
$ sudo /opt/lampstack-8.1.4-0/ctlscript.sh restart
```
default-time-zone='+9:00'
{: .code-example}
db 서버 시간도 한국으로 설정했다.

근데 문제가 생겼다.
mail()이 도저히 되질 않는다.
똑같이 세팅했음에도 이유를 모르겠다. 왜지?

http://3.236.201.25/ 

일단 서버는 열었다.
