---
layout: default
title: Day6
nav_order: 6

parent: Develop Project - W1
grand_parent: P4C
---

##### 해당 게시글은 빡공팟 4기(with TeamH4C)와 관련되어 있습니다

-----

# > 생활 코딩과 공부하기

[https://opentutorials.org/course/62/5135](https://opentutorials.org/course/62/5135) php

기초 문법 이후에 파일 업로드 및 DB와의 연동 방식에 대해 공부를 하고 나면,  
mysql 파트로 넘어가 apache,php,mysql 모두 관여되는 로그인 사이트를 만들 예정이다.  

## 파일

[https://www.php.net/manual/en/function.file.php](https://www.php.net/manual/en/function.file.php) php file function 매뉴얼

```php
<?php
$file = 'readme.txt';
$newfile = 'metamong.txt';
 
if (!copy($file, $newfile)) {
    echo "failed to copy $file...\n";
}

unlink('readme.txt');
file_put_contents($file, 'world, hello');
echo file_get_contents($file);
?>
```

위 예제를 이용하여 실습을 시도하다가 보안 상의 이유인지 일부 함수가 불가능했다.  
파일의 존재와 관여된 `copy` 및 `unlink`와 같은 함수가 제대로 동작하지 않았다.   
생각해보면 임의대로 생성되고 삭제된다면 문제 있는 게 맞는 것 같다.  
관련한 사항에 대해서는 권한과 관련된 공부를 좀 더 해야겠다.

파일의 내용을 읽어오는 `file_get_contents`와 내용을 넣는 `file_put_contents` 함수는 잘 실행되었다.  
다만 좀 더 제어를 위해서는 `fopen`과 `fwrite` 등과 같은 함수를 사용해야 한다.  
관련하여 파일 권한에 대해서 "파일 제어 트러블 슈팅" 영상이 존재했는데,  
열람하려는 other의 파일에 대한 권한이 충분하지 않을 경우에 발생했다.  

지금 상태에서 `chmod 777` 명령어를 통해 파일에 충분한 권한을 주었음에도  
복제와 삭제는 불가능하나 데이터를 읽어오고 넣는 것이 가능하기에,  
정확히 어떠한 원인에 의한 결과 인지를 모르겠다.  
추후 좀 더 알아보아야겠다.

<br><br>

## 디렉토리

디렉토리 리스팅이라는 취약점을 들어본 기억이 있는데 ... 기억 뿐이다(?)

```php
<?php
echo getcwd().'<br />';
chdir('../');
echo getcwd().'<br />';

$dir    = './';
$files1 = scandir($dir);
$files2 = scandir($dir, 1);
 
print_r($files1);
print_r($files2);

mkdir("qwer", 0700);
?>
```

"current working directory" 의 약자로 `getcwd`를 이용하여 지금 작업하는 디렉토리 경로를 얻는다.  
`scandir()`은 작업 중인 디렉토리에 존재하는 파일들을 출력 해주는 함수이다.  
디렉토리를 만드는 `mkdir` 함수는 파일 파트와 동일한 문제인지 실행이 불가능했다.  

<br><br>



## 파일 업로드

HTML 주제에서 배웠던 form 태그를 이용하여 php에 파일을 넘기는 방법으로 진행하였다.  

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>UPLOAD</title>
    </head>
    <body>
        <form  action="upload.php" method="post" enctype="multipart/form-data">
            <input type="file" name="upfile" value="파일을 선택하시오">
            <input type="hidden" name="MAX_SIZE" value=30000>
            <input type="submit" value="업로드">

        </form>
    </body>
</html>

```



![image-20220424162139168](../img/image-20220424162139168.png)

이전에 HTTP를 직접 확인해보았었는데 한 번 더 내가 작성한 인자가 잘 넘어가는지 확인했다.   
`type` 속성의 속성값을 `hidden`으로 넘긴 데이터는 추후 `action`의 대상인 upload.php에서 전달받을 수 있는 값으로 파일 최대 사이즈를 지정하려는 의도로 작성하였다.  
하지만 보내는 HTTP의 정보가 충분히 수정 가능 하기 때문에 보안성이 있다고 볼 수 없다. 만약 파일의 크기를 제한하고 싶을 경우에는 php의 코드에서 작성하는 것이 옳다.

다음은 upload.php를 만들어 넘어간 파일을 다루어보려고 한다.

```php
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
	<?php
		var_dump($_FILES);
		exit;
	?>
    </body>
</html>

```

php 코드를 이용하여 파일을 업로드했을 때 알아야 할 정보가 있다.  
첫쨰로 php로 넘어간 파일은 `$_FILES` 라는 변수에 정보가 담긴다.  
해당 변수값을 `var_dump()`를 이용하여 출력하였을 때 아래와 같은 결과를 볼 수 있다.



![image-20220424163657055](../img/image-20220424163657055.png)

`$_FILES`는 연관배열의 형식으로 파일에 대한 6가지 정보를 담고 있다.  
그 중에서 'tmp_name' 이라는 인자가 존재하는데 해당 인자는 클라이언트에서 서버 측으로 파일을 전송했을 때 서버 측에 저장되는 임시 파일의 경로이다.  
즉, 사용자가 파일을 업로드 하였을 때 특정 위치에 특정 이름을 가진 파일로 바로 저장이 되어지는 것이 아니라, 임시 파일명과 임시 폴더에 저장되어 보관한 뒤 처리가 이루어지는 셈이다.

![image-20220424164517643](../img/image-20220424164517643.png){: width="200"}

추가로 업로드 파일이 2개 이상인 경우 배열이 궁금했는데 마치 구조체 처럼 2개의 정보가 저장되었다.

<br><br>

최종적으로 파일을 서버에 업로드해보는 `upload.php`코드이다.

```php
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <?php
        ini_set("display_errors", "1");
        $uploaddir = '/opt/lampstack-8.1.4-0/apache2/htdocs/upload/';
        $uploadfile = $uploaddir . basename($_FILES['upfile']['name']);
        echo '<pre>';
        if (move_uploaded_file($_FILES['upfile']['tmp_name'], $uploadfile)) {
            echo "파일이 유효하고, 성공적으로 업로드 되었습니다.\n";
        } else {
            print "파일 업로드 공격의 가능성이 있습니다!\n";
        }
        echo '자세한 디버깅 정보입니다:';
        print_r($_FILES);
        print "</pre>";

        ?>
        <img src="upload/<?=$_FILES['upfile']['name']?>">
        </body>
</html>

```

시작에 앞서 익숙하지 않은 함수들을 미리 살펴보자면  
`ini_set()`을 이용하여 php 오류 발생 시 해당 메세지를 출력해주도록 했다.  `php.ini` 파일에도 설정할 수 있지만 해당 함수를 이용하여 특정 php에서만 해당 기능을 사용할 수 있다.  그리고 `basename` 이라는 함수는 보안상의 문제를 해결하기 위해 인자의 값이 특정 파일로만 인식이 되도록 지정한다. 예로 `echo basename("/etc/passwd")`의 실행결과는 "passwd"로 `/`와 같은 경로의 기호를 제외하고 가장 끝에 존재하는 파일의 명만 인식한다. 이를 통해 파일 이름에 `.`과 `/`로 파일 이름에 장난을 치는 문제를 막을 수 있다.

`$uploaddir`은 실제 서버에 업로드 될 경로를 지정하는 변수이다.  
`$uploadfile`은 업로드할 파일의 이름과 함께 절대 경로를 저장해두는 변수이다.  
이 때 `basename`() 를 이용해서 `$_FILES[][]`가 태그되어 있는데 앞서 확인했다시피,  
첫번째 인덱스의 값은 upload.html의 form태그에서 `name`의 속성값으로 지정했던 값이고,  
두번째 인덱스값은 `_FILES`배열의 `name` 인덱스명을 선택한다.  
그럼 해당 배열의 값은 임시로 업로드 된 파일의 명이 지목된다.

그리하여 `move_uploaded_file()`을 이용하여 파일을 실질적으로 업로드 시키는데,  
해당 함수는 파일검사 로직이 포함되어 있기에 업로드 시 해당 함수 사용을 추천한다고 한다.  
조건문 내부에 함수를 넣은 이유는 반환 값이 True/False인 점을 이용하여 성공 여부 판별용이다.  
첫번째 인자는 `$_FILES['upfile']['tmp_name']`를 이용해 임시로 저장되어 있는 파일의 경로를,  
두번쨰 인자는 `$uploadfile`로 서버에 저장될 파일 이름이 포함된 경로를 선택한다.

이후 실행 결과에 따라 값을 확인할 수 있었는데 첫번째 시도는 실패했었다.  
오류 메세지를 통해 권한이 부족하다는 점을 알게 되었고 others의 쓰기 권한을 부여해주었다.  
다만 업로드할 폴더가 거쳐가는 모든 폴더에 쓰기 권한을 부여해야 했기에 마음에 걸렸지만  
아직 방법을 익혀나가는 단계이기에 임시적으로 진행했다.

![image-20220424173811578](../img/image-20220424173811578.png)

추가로 `<img src="upload/<?=$_FILES['upfile']['name']?>">`를 이용하여 업로드한 사진도 출력했는데 기본적으로 설정되어있는 나의 Document Root가 htdocs 폴더이기에 htdocs 폴더에 존재하는 폴더 명부터 작성하여 특정 폴더를 지목할 수 있다. 따라서 upload 폴더에 저장된 파일을 img 태그를 통해 출력할 수 있는 것이다.

<br><br>



## 이미지 다루기

php에서 이미지를 처리하기 위해서는 gd 라이브러리를 이용한다.

```php
<?php
header("Content-type: image/png");
$string = $_GET['text'];
$im     = imagecreatefrompng("upload/button.png");
$color = imagecolorallocate($im, 60, 87, 156);
$px     = (imagesx($im) - 7.5 * strlen($string)) / 2;
imagestring($im, 4, $px, 9, $string, $color);
imagepng($im);
imagedestroy($im);
?>
```

`header()`는 HTTP 통신에서 정보를 전달할 수 있는 함수로 해당 함수보다 윗줄에 코드가 추가되면 오류가 발생하는 특징이 있다.

`imagecreatefrompng` 함수는 사용할 png의 이름경로를 지정하여 이미지를 불러오는 것이다.  
`imagecolorallocate` 함수는 불러온 이미지와 R,G,B 값을 설정하여 색상을 정한다.  
`imagestring` 함수는 이미지, font, x축, y축, 글자, 색을 지정하여 이미지에 글을 적는다.  
`imagepng` 함수를 통해 이미지를 전송하는데 그 형식은 `header`()에 표현된다.  
`imagedestroy` 함수로 이미지 작업을 끝마친다.

`$px`에서 수식이 포함되어 있는데 한글로 해석해서 보면 다음과 같다.  
**{ 이미지 길이 - (한 글자의 폭 * 글자 수) } / 2**  
해당 식을 이해해보면 어떠한 문장을 써도 이미지의 가운데에 글자를 쓰기 위한 x축 값이 되겠다.

![image-20220424180736016](../img/image-20220424180736016.png){: width="300"}

imagecolorallocate에서 사용된 세 숫자는 보다시피 0~255의 값을 가지는 RGB 정수 값이다.  
그런데 수강하고 나니 서버에서 이미지 작업을 하는 것 자체가 좋은 방법은 아니라고 한다.  
그래서 워터마크는 생략하기로 했다. 후.

<br><br>


