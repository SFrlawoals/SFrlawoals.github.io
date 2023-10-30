---
layout: default
title: Day7
nav_order: 7

parent: Develop Project - W1
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 생활 코딩과 공부하기

[https://opentutorials.org/course/62/5175](https://opentutorials.org/course/62/5175) php&mysql 

계획한 로그인 폼을 만들기 위해 공부할 마지막 개념으로 학습 이후에는  
웹을 만들어 나가는 과정에서 필요한 개념들은 찾아가며 공부할 예정이다.

환경 구축 당시 Bitnami를 이용하여 LAMP를 구성하였는데 이 때 설치된 MySQL이 MariaDB였다.  
처음에는 MySQL과 다른 DB라고 생각했으나 Maria DB가 MySQL 서버의 또 다른 fork 버전이라는 정보와 기존 MySQL과의 호환에 전혀 문제가 없다고 하여 그대로 진행하기로 했다.

또한 편의상 Maria DB와 MySQL을 혼용하여 표현하더라도 동일한 데이터베이스 서버를 의미하겠다.  
그럼 처음으로 DB에 접속하는 방법부터 php와의 통신 이전까지 알아보자.

<br><br>

## MySQL 기초

가장 먼저 우분투에서 Bitnami에 포함된 Mysql 프래임에 접근해보았다.

```bash
$ mysql -u root -p 
```

그런데 재미있게도 `mysql`을 설치하라는 안내가 나왔고 많이 당황했다.  
그러나 조금 생각해보니 apache와 같은 서비스도 평소 알고 있던 `Document Root`의 위치가 달랐기에,  
mysql이 관리되는 경로가 다를 것이라고 생각을 했다.

그리하여 find 명령어를 이용해 mysql 파일의 위치를 찾았고 Bitnami LAMP x64 기준

```sh
$ find / -name mysql 2>/dev/null
/opt/lampstack-8.1.4-0/mariadb/bin/mysql
```

해당 경로에 mysql이 존재했다.  
아마도 `PATH`의 경로까지 지정해두지 않아 설치되어 있음에도 명령어 실행이 되지 않았던 것 같다.  

```sh
$ ./mysql -u root -p
Enter password: 
```

이후 접근 계정명과 비밀번호를 입력한 후에 데이터베이스 서버에 접속할 수 있었다.

<br>

데이터베이스 서버에서 알아두어야 하는 것은 첫 번째, 데이터베이스 서버이다.  
MySQL, MariaDB와 같은 DB 그 자체를 의미하여 테이블 정보를 담는 데이터베이스와는 다른 의미이다.  
두번째로, 데이터베이스 이다.  
데이터 정보를 담는 테이블들의 폴더 개념으로써 사용 데이터베이스를 선택한 후 테이블에 접근한다.  
세번째로, 테이블이다.  
데이터 정보가 담기는 표와 같은 개념으로 행,열로 구성되어 설정된 기준에 따라 데이터가 저장된다.  
실질적으로 데이터 정보를 담고 있는 곳은 테이블이고, 해당 테이블들이 묶여있는 곳이 데이터베이스, 그리고 이러한 서비스가 제공되는 것이 데이터베이스 서버이다.

<br>

그럼 테이블을 생성하기 위해 데이터베이스 서버에 접속한 상태이므로 데이터베이스 자체를 만들 차례이다. 그리고 해당 작업을 위해서는 "SQL" 이라는 것이 사용되는데, Structured Query Language의 약자로 데이터베이스 서버에서 데이터들을 관리하기 위해 사용되는 언어이다.

특정 파일에 접근하기 위해서 특정 폴더로 먼저 접근을 하게 되는데 이 때 `USE` 라는 구문을 사용한다.  
그리고 접근보다 이전에 해당 폴더를 먼저 만들어야 하는데 `mkdir`과 같은 역할을 하는 것이 `CREATE` 구문이다. 참고로 데이터베이스 삭제는 `DROP DATABASE [데이터베이스명]` 이다.

```sql
$ CREATE DATABASE my_web CHARACTER SET utf8 COLLATE utf8_general_ci;
```

`CREATE DATABASE`를 통해 my_web 라는 이름의 데이터베이스를 생성하였고,  
CHARACTER SET과 COLLATE 설정을 통해서 한글 데이터가 입력/검색될 수 있도록 설정하였다.  
그리고 `SHOW` 구문을 통해 생성된 데이터베이스와 테이블들을 확인할 수 있다.



```sql
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
| my_web             |
+--------------------+
5 rows in set (0.001 sec)
```



다음은 테이블 생성을 위해서도 `CREATE` 구문을 이용하여 컬럼명과 데이터 타입 및 설정을 지정하면 된다.

```sql
MariaDB [my_web]> CREATE TABLE userinfo (
	idx  int(4) NOT NULL AUTO_INCREMENT,
	useremail varchar(40) NOT NULL,
	userid  varchar(10) NOT NULL,
	userpw  varchar(20) NOT NULL,
	track  varchar(20) NOT NULL,
	PRIMARY KEY (idx)
);

MariaDB [my_web]> SHOW tables;
+------------------+
| Tables_in_my_web |
+------------------+
| userinfo         |
+------------------+

MariaDB [my_web]> DESC userinfo;
+-----------+-------------+------+-----+---------+----------------+
| Field     | Type        | Null | Key | Default | Extra          |
+-----------+-------------+------+-----+---------+----------------+
| idx       | int(4)      | NO   | PRI | NULL    | auto_increment |
| useremail | varchar(40) | NO   |     | NULL    |                |
| userid    | varchar(10) | NO   |     | NULL    |                |
| userpw    | varchar(20) | NO   |     | NULL    |                |
| track     | varchar(20) | NO   |     | NULL    |                |
+-----------+-------------+------+-----+---------+----------------+
5 rows in set (0.013 sec)

```

테이블 정보를 확인하기 위해서는 `DESC` 구문을 이용한다.

칼럼을 추가할 때 칼럼명, 데이터타입, 특성 순으로 작성하게 되는데 Type이 int인 경우는 정수만,  
varchar(N)인 경우는 문자의 길이가 0~N 까지만 문자 입력이 가능해진다.  
`NOT NULL`은 해당 데이터를 공백으로 둘 수 없음을 의미하고 `auto_increment` 설정을 해두면,  
사용자가 설정하는 것이 아닌 자동으로 증가하는 값을 가지게 된다.  

`PRIMARY KEY` 설정은 고유 식별자를 지정하여 해당 테이블에서 고유한 VALUE가 된다.   
중복이 불가능한 `UNIQUE`와 `NOT NULL`이 설정되는 것으로 알고있다.  
`PK`와 `FK`에 대한 존재는 알고 있었지만 실질적인 사용법을 모르기에 추후 알아봐야겠다.  

데이터베이스와 테이블은 생성 이후에도 추가하거나 수정 및 삭제가 가능하므로  
내가 최종 프로젝트에 사용할 칼럼은 시간을 들여서 고려해봐야겠다.

<br>
<br>

## 데이터 추가, 수정, 삭제, 검색

`INSERT INTO` 구문은 테이블에 데이터를 추가할 수 있다.  
컬럼명을 명시하고 데이터를 넣는 방법과 생성된 컬럼명 순서를 기준으로 데이터를 넣는 방법이 있다.

`SELECT` 구문은 테이블의 데이터를 조회할 수 있다.  
`FROM`절과 함께 사용되어 FROM절 앞에는 출력할 컬럼을, 뒤에는 테이블을 지정한다.

```sql
MariaDB [my_web]> INSERT INTO userinfo (useremail,userid,userpw,track) VALUES ('qwer@gmail.com','test1id','test1pw','sys');
Query OK, 1 row affected (0.014 sec)

MariaDB [my_web]> INSERT INTO userinfo VALUES ('4','asdf@gmail.com','test2id','test2pw','web');
Query OK, 1 row affected (0.008 sec)

MariaDB [my_web]> SELECT * FROM userinfo;
+-----+----------------+---------+---------+-------+
| idx | useremail      | userid  | userpw  | track |
+-----+----------------+---------+---------+-------+
|   1 | qwer@gmail.com | test1id | test1pw | sys   |
|   4 | asdf@gmail.com | test2id | test2pw | web   |
+-----+----------------+---------+---------+-------+
2 rows in set (0.001 sec)
```

idx 칼럼을 auto_increment로 설정했기에 따로 값을 지정하지 않아도 값이 지정된다.

<br>

`UPDATE` 구문은 특정 정보를 수정할 수 있는데 보통 `WHERE` 절의 조건과 함께 사용한다.

```sql
MariaDB [my_web]> UPDATE userinfo SET track='web' WHERE userid='test1id';
Query OK, 1 row affected (0.010 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [my_web]> SELECT * FROM userinfo;
+-----+----------------+---------+---------+-------+
| idx | useremail      | userid  | userpw  | track |
+-----+----------------+---------+---------+-------+
|   1 | qwer@gmail.com | test1id | test1pw | web   |
|   4 | asdf@gmail.com | test2id | test2pw | web   |
+-----+----------------+---------+---------+-------+
2 rows in set (0.000 sec)
```

<br>

`DELETE` 구문은 데이터 행을 삭제한다. 

```sql
MariaDB [my_web]> DELETE FROM userinfo WHERE useremail='asdf@gmail.com';
Query OK, 1 row affected (0.010 sec)

MariaDB [my_web]> SELECT * FROM userinfo;
+-----+----------------+---------+---------+-------+
| idx | useremail      | userid  | userpw  | track |
+-----+----------------+---------+---------+-------+
|   1 | qwer@gmail.com | test1id | test1pw | web   |
+-----+----------------+---------+---------+-------+
1 row in set (0.000 sec)
```



SQL 기본 구문에 대해서 알아보았고 다음부터는 php에서 DB로 쿼리를 전송하는 방법을 배워야겠다.



<br><br><br>

----

# > 한번 알아보았습니다.

- 웹 해킹 공부를 시작하기 전에 웹이 익숙해지기 위해서 sqld 자격증을 준비하고 있었다. 해당 과정에서 관계형 데이터베이스(RDBMS)의 존재부터 mongoDB와 같은 비-관계형 데이터베이스 등을 접했었다. 또한 기본적인 SQL 문에 대해서 일부 배웠지만 실습 환경을 통해 학습하지 못하여 이번 기회에 직접 해볼 수 있었다.


<Br>
-  MariaDB라는 이름을 처음 접하여 조금 알아보기로 했다.

  >**MariaDB의 실행 프로그램들과 유틸리티는 모두 MySQL과 이름이 동일하며 호환**
  >
  >MySQL을 포크해서 사용되기 때문에 오픈소스로써의 상용 버전이 없음
  >
  >MySQL 엔진+PerconaServer 엔진이 사용됨
  >
  >MySQL의 대체품으로 불림.

<br>

- use로 선택한 데이터베이스를 나가는 구문이 있나..?

> 못찾아서 그냥 quit으로 나간 다음 다시 선택했다.


