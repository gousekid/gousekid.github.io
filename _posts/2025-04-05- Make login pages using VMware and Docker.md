---
title: Make login pages using VMware and Docker
excerpt: 
categories:
  - hacking
tags:
  - hacking
last_modified_at: 2025-04-05
---
## VMware에 ubuntu 22.04 LTS 설치

VMware를 사용하여 ubuntu 22.04 LTS를 설치하였다. 24.04를 설치하고 싶었지만, 현재 24.04의 경우에는 설치에 오류가 발생하는듯 하다.
내가 목표하는 바는 아래와 같다.
1. Ubuntu 22.04 LTS 설치
2. Docker를 활용한 webserver 구동 (Apache2, PHP)
3. Docker내에 web서버 파일과 VMware와의 연동

## Docker를 활용한 webserver 구동
Docker를 처음 써보는 입장에서 Docker는 다음과 같은 개념으로 이해된다.
Docker는 하나의 배포를 위한 image를 생성하고, 이 image를 활용하여 container를 구동한다.
container가 구동된 이후부터는 이후 독립적이다.
Docker 내부에는 운용하고자 하는 OS까지도 설정할수 있다. 즉, 현재 사용자의 OS와 상관없이 동일한 운용을 하기위해 개발된 시스템으로 보여진다.

1. Docker 이미지 생성
Docker 이미지는 Dockerfile을 통해 생성할 수 있다. Dockerfile은 Docker image가 어떻게 구성되는지에 대해 적혀있는 text 파일이다. 간단하게 다음과 같이 구성된다.

```
FROM ubuntu:22.04
LABEL creator="ywkim"
LABEL version="0.0.1"

WORKDIR /app


ENV DEBIAN_FRONTEDN=noninteractive
RUN ln -fs /usr/share/zoneinfo/Asia/Seoul /etc/localtime
RUN apt-get update && apt-get install -y apache2 php libapache2-mod-php

EXPOSE 80

ENTRYPOINT ["/usr/sbin/apache2ctl","-D","FOREGROUND"]

```

위에서 FROM은 Docker의 가장 base가 되는것으로 보여진다. ubuntu22.04 Os로 구동한다는 뜻.
LABEL은 다른 예제에서 확인한것인데, 나름대로 제작자와 버전을 작성해 보았다.
WORKDIR 은 Docker에서 작업이 진행될때 사용되는 루트 라고 보면된다.

ENV의 같은 경우에는 환경변수를 의미한다. 일반적으로 우분투에 어떤것을 설치할때 사용자와 인터렉트가 필요한 경우가있다. 그러나 도커의 경우에는 상호작용을 할 수 없기 때문에 다음과 같이 noninteractive로 설정하여 가장 적절한것을 설치하게 해주었다.
이를 하게된이유는, php를 설치할 때 시간대역을 설정해주는 option이 필요하기 때문이다. 
아래의 RUN은 명령을 실행하는것으로, 서울의 시간과 우분투의  localtime을 link해서 시간을 설정하였다.
마지막으로 필요한 apache2 php 등을 설치하였다.
혹시 몰라 포트 80을 명시해주었고, 마지막으로 apache를 실행 시키는 명령을 넣었다.
여기서 중요한점은, docker가 background로만 실행하게 된다면, 종료된다고한다. 그렇기 때문에 일반적으로 apache는 background로 실행시키지만, 도커에서는 foreground에서 실행하게 작성하였다.
그리고 아래와 같은 명령으로 Docker이미지를 생성하였다.
```
sudo docker build -t webtest .
```


2. Docker 컨테이터 구동
도커 컨테이너 구동을 위해 다음과 같이 실행시켰고, 외부에서 파일을 수정할 수 있도록 특정 폴더와 링크를 시켜두었다. 또한 포트포워딩도 진행 하였다.
```
sudo docker run -v /home/test/Desktop/webserver/html/:/var/www/html -p 80:80  webtest

```


## PHP를 통한 로그인 페이지 구축
PHP를 통한 로그인 페이지는 다음과 같이 구성하려고 한다.
index.html -> loginphp.php ------- login_success.html (성공)
                        ㄴ-- login_fail.html(실패)


1. index.html
index에서 중요한것은 아래와 같은 form 문이다. from 문을 통해 post 방식으로 loginphp.php로 로그인관련 정보를 전송하게 하였다.
```
<form method="post" action=" loginphp.php ">
```
2. loginphp.php
loginphp.php에서는 if문을 활용하여 비교하였다. 이때 다른언어와는 다르게 === 를 써야 엄밀하다는 정보를 확인하여 === 를 활용하여 string을 비교하였다.
페이지 이동은 hearder라는 함수를 통해 진행하였는데, php파일을 작성하면서 가장 문제가 많았던것은, c언어와 같이 ;로 항상 닫아줘야한다는 사실이였다.

```
<?php


$ID_TRUE = "admin";
$PW_TRUE = "admin1234";

$id = $_POST["id"];
$pw = $_POST["pw"];

if ($id === $ID_TRUE && $pw === $PW_TRUE)
{
    header("Location: ./login_success.html");

}
else
{
    header("Location: ./login_fail.html");
}


?>
```


## 결과 스크린샷
결과는 아래와 같다.
### index
![](\assets\images\2025-04-05- Make login pages using VMware and Docker\login.png)
### 로그인성공
![](\assets\images\2025-04-05- Make login pages using VMware and Docker\success.png)

### 로그인 실패
![](\assets\images\2025-04-05- Make login pages using VMware and Docker\fail.png)

