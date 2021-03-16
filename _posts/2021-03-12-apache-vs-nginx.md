---
title: "Apache vs Nginx"
date: 2021-03-12
categories: web server
---

세상에는 다양한 종류의 웹서버가 존재한다. 

웹개발이나 웹서비스를 할 때는 용도나 특성에 맞는 웹서버를 선택해야 할 것이다. 

이번에는 Apache 와 Nginx 에 대해서 좀 알아보겠다.
<br><br><br>

## 점유율
![source_hostadvice](https://tgombseojh.github.io/img/apache_vs_nginx.jpg)

서비스의 특성에 따라 점유율이 다를테니 점유율은 참고만 하도록 하자.
<br><br><br>

## 특징
|비교|Apache|Nginx|
|---|---|---|
|출현시기|1995|2004| 
|정적컨텐츠|전통적 파일기반 처리|이벤트, 비동기, 논블로킹 처리| 
|동적컨텐츠|서버 자체 처리|외부 처리(프록시)|  
|아키텍처| ![architecture_apache](https://tgombseojh.github.io/img/architecture_apache.jpg) | ![architecture_nginx](https://tgombseojh.github.io/img/architecture_nginx.jpg) |
|구성방식|분산, 중앙 집중 선택|중앙 집중형.. 클라우드, 가상화, MSA 에서는 경량화와 성능보장이라는 측면에서 장점이 될 수 있다| 
|모듈 확장성 / 보안|60+다양한 모듈, 선택적 활성, 다양한 디자인과 확장 가능. DDos 방어 제공.|다른 코어 모듈을 동적으로 로딩 불가. 옵션 최소화. 보안 관련 기술 문서 제공.| 
<br>

## Nginx 설치 및 설정
```ubuntu
sudo apt-get install nginx

sudo vi /etc/nginx/nginx.conf

http {} 블록 끝에 구문 추가
include /etc/nginx/sites-enabled/*.conf; // sites-enabled 디렉토리에서 서버 블록을 찾도록 지시
server_names_hash_bucket_size 64; // 도메인이름 분석하는데 할당되는 메모리 양

/etc/nginx/sites-available/도메인이름(프로토콜 제외).conf 아래 내용 입력
server {
  listen  80;
  server_name 도메인이름;

  location / {
    root  /var/www/html; //vsftpd 홈디렉토리
    index  index.html index.htm index.nginx-debian.html;
    try_files $uri $uri/ =404; a
  }

  error_page  500 502 503 504  /50x.html;
  location = /50x.html {
    root  /usr/share/nginx/html;
  }
}

sudo ln -s /etc/nginx/sites-available/도메인.conf /etc/nginx/sites-enabled/도메인.conf

sudo systemctl restart nginx

sudo ls /var/log/nginx // access.log error.log
sudo tail -f /var/log/nginx/access.log
```

참고사이트: [Nginx.org](https://nginx.org/en/docs/)

참고사이트: [Nginx](https://coding-start.tistory.com/381)

참고사이트: [Nginx](https://juneyr.dev/nginx-basics)
