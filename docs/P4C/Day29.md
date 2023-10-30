---
layout: default
title: Day29
nav_order: 1

parent: Hacking Project - W5
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > DVWA 환경 구축하기

Damn Vulnerable Web Application 웹 모의해킹 환경이라고 한다.



결과적으로 구축에는 완료했고 이전 개발 프로젝트 덕에 수월하게 진행할 수 있었다.

![image-20220516134452228](..//img/image-20220516134452228.png)

<br>

나는 이전 개발 프로젝트에서 Bitnami를 이용하여 AMP를 구축해두었기에 인터넷에 나와있는 AMP 구축 방식과는 좀 달랐다. 운영체제는 우분투 20.04 LTS를 이용하였고 DB는 MariaDB를 사용하였다. 아래는 Bitnami를 이용하여 AMP가 구축된 환경에서 DVWA를 구축했던 명령어이다.

```bash
$ cd /opt/lampstack-8.1.4-0/apache2/htdocs
$ wget https://github.com/digininja/DVWA/archive/master.zip
$ sudo apt-get install unzip
$ sudo unzip master.zip
$ mv DVWA-master/ DVWA
$ sudo chmod -R 755 DVWA
$ sudo chown -R www-data:www-data DVWA
$ cd /DVMA/config
$ sudo cp config.inc.php.dist config.inc.php
$ sudo vi config.inc.php
```
<div class="code-example">
    $_DVWA[ ‘db_server’ ]   = ‘localhost’;<br>
	$_DVWA[ ‘db_database’ ] = ‘dvwa’;<br>
	$_DVWA[ ‘db_user’ ]     = ‘dvwa’;<br>
	$_DVWA[ ‘db_password’ ] = ‘p@ssw0rd’;<br>
	$_DVWA[ ‘db_port’] = ‘3306’;<br>
</div>
이 단계 중에서 `config.inc.php` 수정이 제일 중요한데 해당 파일 내부에서 mariaDB일 경우에 `README.md`를 참조하도록 되어있다. 참조 해보니 mariaDB에서는 root 권한을 사용할 수 없어서 따로 설정을 해야한다고 안내되어 있다. 그래서 mysql을 실행하여 적용해야 하는 사항이 존재하는데 참조 파일을 그대로 따라했다.

```sql
mysql> create database dvwa;
Query OK, 1 row affected (0.00 sec)

mysql> create user dvwa@localhost identified by 'p@ssw0rd';
Query OK, 0 rows affected (0.01 sec)

mysql> grant all on dvwa.* to dvwa@localhost;
Query OK, 0 rows affected (0.01 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

<br>

```bash
$ sudo vi /opt/lampstack-8.1.4-0/php/etc/php.ini
```
<div class="code-example">
	allow_url_include = On 
</div>


<br>
<br>

```bash
$ sudo /opt/lampstack-8.1.4-0/ctlscript.sh restart
```

위까지 설정을 하고 `localhost/DVWS/setup.php` 에 접속을 해보면 아래와 같은 정보가 뜨는데 이 중에서 빨간색 "NO" 부분을 해결하기 위해 아래 명령어를 작성했다
```bash
sudo chmod 666 /opt/lampstack-8.1.4-0/apache2/htdocs/hackable/uploads
sudo chmod 666 /opt/lampstack-8.1.4-0/apache2/htdocs/external/phpids/0.6/lib/IDS/tmp/phpids_log.txt
sudo chmod 777 /opt/lampstack-8.1.4-0/apache2/htdocs/config
```

<br>

recaptcah를 제외하고 모두 Yes로 바뀐 것을 확인하고 Setup하니 완성되었다. 이후에 low 레벨을 풀어야하기에 아래와 같이 low레벨로 설정하였고 내일부터 제대로 풀 예정이다. bob 자소서도 써야하니 시간 안배를 잘해야겠다.

![image-20220516145246427](../img/image-20220516145246427.png)
