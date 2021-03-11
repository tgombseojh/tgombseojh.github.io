---
title: "Apache vs Nginx"
date: 2021-03-12
categories: web server
---

세상에는 다양한 종류의 웹서버가 존재한다. 

웹개발이나 웹서비스를 할 때는 용도에 맞는 웹서버를 선택해야 할 것이다. 

이번에는 Apache 와 Nginx 에 대해서 좀 알아보겠다.
<br><br><br>
## 점유율
![source_hostadvice](https://tgombseojh.github.io/img/apache_vs_nginx.jpg)

서비스의 특성에 따라 점유율이 다를테니 점유율은 참고만 하도록 하자.
<br><br><br>

## 특징
|처리방식|Apache|Nginx|
|---|---|---|
|정적컨텐츠|전통적 파일기반 처리|이벤트, 비동기, 논블로킹 처리| 
|동적컨텐츠|서버 자체 처리|외부 처리(프록시)| 
<br>

|아키텍처|Apache|Nginx|
|---|---|---|
|그림|![architecture_apache](https://tgombseojh.github.io/img/architecture_apache.jpg)|![architecture_nginx](https://tgombseojh.github.io/img/architecture_nginx.jpg)| 


