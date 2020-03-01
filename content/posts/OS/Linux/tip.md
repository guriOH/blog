---
title: Useful command
date: 2019-12-12 09:12:12
tags:
---

특정 포트를 사용하는 프로세스 검사

* lsof -i :[port]


파일의 전체 내용출력
* awk '{print}' [File]

지정된 문자열을 포함하는 레코드만 출력
* awk '/STR/' [File]

특정 포트를 사용하는 프로세스 정보보기
* lsof -i TCP:22

특정 명령어가 사용하는 포트 보기
* lsof -c httpd