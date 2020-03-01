---
title: 카프카(Kafka) 실행시키기 - 스크립트
date: 2019-12-01 17:46:57
tags: [Kafka]
categories:
 - [BigData]
---



![](../../image/kafka/1.jpeg)



이제 카프카를 실행 시켜 보자!!

목표는 아래와 같다.

1. 카프카를 설치해서 빌드된 스크립트를 이용하여 카프카의 동작여부를 확인해보자.
2. 최종적으로는 Intellij를 통해 빌드해보며 어떤 식으로 돌아가는지 코드로 확인해 보자.


# 카프카(Kafka) 실행시키기 - 스크립트

# Step.1 DownLoad

카프카 공식 홈페이지에서 다운로드!!

현재기준 kafka 최신 버전은 2.3.0이다.

```
$ tar -zvxf kafka_2.12-2.3.0.tgz 
$ cd kafka_2.12-2.3.0.tgz 
```

다운로드 받아서 압축을 푹어준다.



# Step.2 실행시키기

카프카를 실행 시켜주면 된다.

먼저 카프카는 기본적으로 Zookeeper에서 관리가 되고 있기 때문에 Zookeeper가 실행이 되어야만 동작한다.

따라서 Kafka실행전 Zookeeper를 실행해야 한다.



먼저 두 어플리케이션을 실행시키기 전에 설정파일을 일부 수정한다.

```
zookeeper.properties

# the directory where the snapshot is stored.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
# disable the per-ip limit on the number of connections since this is a non-production config
maxClientCnxns=0

server.1 = 127.0.0.1"2888:3888 //.[number] 는 아이디 값.

```

```
server.properties
.
.
.
############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=1

############################# Socket Server Settings #############################
listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://127.0.0.1:9092

############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=127.0.0.1:2181

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=6000
```

위 와 같이 zookeeper에 서버정보를 추가하고 (복수개 설정가능)

Kafka의 server 프로퍼티에도 해당 zookeeper의 정보를 입력해주면 된다.

```
$ zookeeper-server-start.sh config/zookeeper.properties
$ kafka-server-start.sh config/server.properties
```

그리고 두 어플리케이션을 실행한다.

이제 Topic을 생성 후 Producer 인풋 메시지를 저장하고 Consumer로 받아보자.

```
kafka-topics.sh --create --zookeeper 127.0.0.1:2181 --replication-factor 1 --partitions 1 --topic test

Created topic test.
```

다운받은 소스에서 토픽을 생성하는 스크립트를 이용하여 Test 라는 토픽을 생성하였다.

> Topic은 추가로 생성, 삭제가 가능하다



이제 Topic과 마찬가지로 생산자와 소비자 스크립트를 이용해서 메시지를 교환하여 보자

```
kafka-console-producer.sh --ber-list 127.0.0.1:9092 --topic test
>Test
>Hi
>
```

위와 같이 생산자가 메시지를 입력한다.



```
kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic test --from-beginning
Test
Hi

```

--from-beginning 은 처음부터 메시지를 읽겠다는 뜻이고 입력한 메시지를 읽어온것을 확인 할 수 있다.