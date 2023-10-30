---
layout: default
title: Day12
nav_order: 5

parent: Develop Project - W2
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----

# > 2차 시도

어제에 이어 게시물을 띄우기 위해 DB도 만들고 출력도 했다.



![image-20220429144143070](../img/image-20220429144143070.png)



중간 코드 점검으로 아직 DB 구상을 제대로 끝마치지 못했다.  
그래서 일단 기본적으로 필요한 컬럼만 생성했다.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <link rel="stylesheet" href="/css/mstyle.css">
        <title>P4CSS</title>
    </head>
    <body>
        <div id ="header_container">
            <div id="left-header">
                <a href="main.php" style="font-size: 150px">Story Shared</a>
            </div>
            <div id="right-header"><p ><br/>
                <?php   session_start();  if(isset($_SESSION['username']))  echo $_SESSION['username'];  else echo"<script>location.replace('/login.html')</script>"?> 님
                </p>
                <a href="logout.php" >LOGOUT</a>
            </div>
        </div>
<?php
    include('db_conn.php');
    $conn = db_connect();
    $sql ="SELECT * FROM story ORDER BY idx desc";
    $result = mysqli_query($conn, $sql);
    $total = mysqli_num_rows($result);
?>

        <div id="body_container">
            <div id="left-body">
                <input class="load_btn" type="button" onclick="alert(1);"><br/>
                <input class="load_btn" type="button" onclick="alert(1);">

            </div>
            <div id="right-body">
                <p>
                    <form action="search.php" method="post">
                        <select style="width: 70px;" name="track">
                            <option value="title">제목</option>
                            <option value="contents">내용</option>
                            <option value="writer">작성자</option>
                        </select>
                        <input type="text" name="value" placeholder="Search">
                        <input class="btn_search" type="submit" value="검색">
                    </form>
                </p>
                <p style="text-align:right">
                    <a href="write.php" style="font-size: 40px;">글쓰기&nbsp;</a>
                </p>
                <table >
                    <thead>
                        <th width = "100">번호</th>
                        <th width = "200">날짜</th>
                        <th width = "800">제목</th>
                        <th width = "200">작성자</th>
                        <th width = "100">조회수</th>
                    </thead>
                    <tbody>
                        <?php
                            while($row = $result->fetch_array())
                            {
                              $title=$row['title'];
                              if(strlen($title)>15)
                              {
                                $title=substr($row['title'],0,15)."...";
                              }
                        ?>
                        <tr class='<?php echo $row['track']; ?>'>
                            <td width="100"><?php echo $row['idx']; ?></td>
                            <td width="200"><?php echo $row['createtime']?></td>
                            <td width="800"><a href="read.php?idx=<?php echo $row["idx"];?>"><?php echo $title;?></a></td>
                            <td width="200"><?php echo $row['writer']?></td>
                            <td width="100"><?php echo $row['hit']; ?></td>
                        </tr>
                        <?php } ?>
                    </tbody>
                </table>
            </div>
        </div>
    </body>
</html>
```



<br>
<br>
<br>

-----


# > 3차 시도


게시물을 php로 읽어보았다.

![image-20220429144546499](../img/image-20220429144546499.png)

하염없이 하찮아 보이기는 하지만 되긴 했다..;;  
무한 f5 방지와 추천 기능은 대문 고치고 돌아와야겠다.

<br>
<br>
<br>

-----

# > 대문 단장하기

어느 정도 기본적인 틀은 갖추어진 것 같아서 조금 더 세세한 부분을 터치하려고 한다.  
메인 페이지로 넘어가기 전 단계인 로그인과 회원가입에 추가할 부분이다.

1. 비밀번호 암호화하여 DB에 저장하기
2. 이메일 인증하기
3. 비밀번호 찾기 기능 추가하기(-> 이메일 인증되면 비밀번호 바로 수정가능)

<div class="text-red-000">
잘되던 VM이 갑자기 멈춰서 내 심장도 멈추는 줄 알았다.<br>
모든게 날라갈뻔 했는데 살려서 다행이다 ㅠㅠㅠㅠㅠㅠ  <br>
</div>
[https://github.com/Jminis/P4C](https://github.com/Jminis/P4C) <- 여기에 백업하면서 해야겠다...  

## 비밀번호 암호화하기

```sql
$sql = "INSERT INTO userinfo (useremail,userid,userpw,username,usertrack,createtime)
VALUES('$uemail','$uid',sha2('$upw',256),'$uname','$utrack',now())";
```
검색해보니 `password()`를 사용하라는 글도 있었는데 DB에서 제공하는 함수를 사용하기로 했다. `SHA2` 함수에서 256bit도 지원하므로 단방향으로 암호화 한 다음에 로그인 시에 입력받은 비밀번호도 암호화 하여 저장된 비밀번호와 대조하는 식으로 인증한다.

## 이메일 인증하기

대부분 `node.js` 로 구현하라고 정보가 나와있어서 자바스크립트와 다른 게 없겠거니 했는데 정보를 찾아보면 찾아볼수록 두 언어가 다름을 알아채고 php로 구현하기로 했다.  
근데 다른 사람은 다 되는 php의 mail()이 나는 도저히 안되서 삽질 중이다.

점심에 시작했는데 저녁을 먹고도 3시간이 훌쩍 지났다.  
php의 mail()을 이용하여 이메일을 발송할 수 있고,  
난수를 생성하여 db에 올려둔 뒤 검사하는 방법도 생각해냈다.  
그런데 이상한 난관에서 삽질을 하고 있다.

1. 일단 메일 발송이 너무 느리다. 30초 정도 걸린다.
2. 인증 이후에 입력 값을 바꾸면 어떡하지?
3. 인증 시간에 제한은 어떻게 만들 것인지?
4. 난수를 때려 맞추면 어떡하지?

해결할 때까지 잠 안 자야겠다.

<br>

php 시간차이 구하는 법 찾다가 여기에 잠들다...
