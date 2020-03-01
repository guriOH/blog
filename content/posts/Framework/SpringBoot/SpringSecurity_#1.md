---
title: 스프링 시큐리티 이해하기
date: 2019-12-03 13:32:19
tags:
---

![스크린샷 2019-12-05 오전 11.13.11](/Users/hoonoh/Desktop/스크린샷 2019-12-05 오전 11.13.11.png)

스프링시큐리티 시나리오 

* 인증
* 권한체크



#### Authentication 객체

* 이름
* 권한
* 인증여부





AuthenticatioFilter 에서 사용자 정보를 꺼내 Authentication 객체를 만들고,

AuthenticationProvider에 전달한다.

AuthenticationProvider에서는 실제 인증이 이루어 지고, 인증 결과를 Authentication에 담아 SecurityContextHolder에 저장 성공 여부에 따라 handler를 실행한다.














