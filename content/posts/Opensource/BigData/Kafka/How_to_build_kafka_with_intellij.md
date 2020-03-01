---
title: 카프카(Kafka) 실행시키기 - Intellj
date: 2019-12-01 17:46:57
tags: [Kafka]
categories:
 - [BigData]
---


앞선 내용에서 Kafka에서 제공하는 스크립트를 통해 카프카를 실행 시켜 보았다.

오늘은 카프카를 IDE를 통하여 실행을 시킴으로서 Kafka가 내부적으로 어떻게 동작을 하는지 더 정확히 확인을 해보려고 한다



오늘의 목표

* Intellij를 이용하여 Kafka 빌드



우선 Kafka 빌드에 앞서 선행적으로 준비가 되어야 할 것이 있다.

* git 
* Java 8
* IntelliJ

위 환경이 준비가 되었다는 가정하에 문서를 보면 된다.

> 인텔리제이의 경우 Scala, Gradle을 플러그인 형태로 아주 쉽게 다운로드 받을 수 있다.
>
> 만약 이클립스를 사용한다면 위 두가지를 다운로드 받거나 플러그인이 있다면 추가해서 사용하자.
>
> 카프카는 Scala 로 작성된 어플리케이션 이다.


# 카프카(Kafka)  실행시키기 with Intellij

## Step-1. Git clone

```
git clone https://github.com/apache/kafka.git
```

위 명령어를 통해 Kafka 소스를 내려 받는다.



## Step-2. Build

카프카의 실행에 앞서 실행에 필요한 서드 파티 파일들을 받아야 한다.

```
./gradlew clean
./gradlew jar
```

## Step-3. Run Kafka

![](/image/kafka/3.png)

```
VM Options
-Dkafka.logs.dir=/tmp/kafka -Dlog4j.configuration=file:config/log4j.properties

Program arguments
config/server.properties
```

VM Options은 server.properties 에 설정해둔 로그파일 저장 위치와 log4j 설정 정보를 입력한다.



## Error-#1

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
SLF4J: Failed to load class "org.slf4j.impl.StaticMDCBinder".
SLF4J: Defaulting to no-operation MDCAdapter implementation.
SLF4J: See http://www.slf4j.org/codes.html#no_static_mdc_binder for further details.
```

실행했을때 위와 같은 에러로그가 발생한다면

```
kafka/build.gradle
project(':core') {
  dependencies {
     compile project(':clients')
     compile libs.slf4jlog4j12
     ....
  }
}
kafka/gradle/dependencies.gradle
versions += [
   ....
   zstd: "1.4.3-1",
   slf4jlog4j12: "2.0.0-alpha1"
]
libs += [
  .....
  httpclient:     "org.apache.httpcomponents:httpclient:$versions.httpclient",
slf4jlog4j12: "org.slf4j:slf4j-log4j12:$versions.slf4jlog4j12"
]
```

위와 같이 'slf4jlog4j12'를 추가.


Ref. https://medium.com/@chandreshpancholi007/how-to-setup-apache-kafka-source-code-on-intellij-b204966d7c2