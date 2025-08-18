---
layout: post
author: "Yan"
catalog: true
subtitle: "고가용성 시스템을 설계해보자"
header-img: "img/header/spring.jpg"
title: "Heartbeat란?"
date: 2025-08-18 11:40:08 +0000
categories:
  - Arcitecture
tags:
  - Heartbeat
comments: true
---

# [Heartbeat](https://en.wikipedia.org/wiki/Heartbeat_(computing))

Heartbeat란, 분산 시스템 환경 등에서, 서버나 서비스나 어떤 컴포넌트가 정상적으로 작동하고 있는지 주기적으로 체크하는 메세지 교환 방식이다. 지금 현재 실행 중인지, 네트워크에 접속 되어 있는지 node 들에 정기적으로 신호를 보내서 검증하는 방식이다. 일정 기간 안에 하트비트 체크 메세지가 응답이 안 올 경우에 시스템이 실패중이라고 파악할 수 있다. 

## Heartbeat 가 중요한 이유: fault tolerance, reliablility, availability
  - 실패 감지
  - 헬스 체크
  - 가용성 있는 컴포넌트를 파악하여 **로드밸런싱**에 활용
  - 네트워크 파티션 감지하여 잠재적인 네트워크 문제 파악
  - 일관성 유지

## heartbeat message로 어떤 것을 사용할까?
  - seqence number로 예상한 번호가 응답으로 돌아오는지 확인하는 방법
  - node/component의 id값 부여해서 unique id를 확인하는 방법
  - timestamp로 freshness 확인하는 방법
  - payload/data로 버젼 넘버나 설정 정보, 메세지 받는 쪽이 보내는 쪽의 상태를 체크할 수 있을만한 정보 넘겨주는 방법
  - ack 메세지 검증하는 방법
  - timeout limit 정해놓는 방법

## Heartbeat Protocol의 종류

### 1. Simple Heartbeat Protocol

간단한 하트비트 메커니즘이 필요한 상황에서, 단순 메세지를 교환하여 정기적으로 노드의 가용성과 활성상태를 파악하는 방식

![](https://media.geeksforgeeks.org/wp-content/uploads/20240313170247/Simple-Heartbeat-Protocol-(SHP).webp)

### 2. Ping/Echo Protocol

한 쪽에서 Ping을 보내고 수신 노드에서 echo 응답을 기다리는 것을 뜻하며  
네트워크 수준의 통신 (ICMP)를 사용하여 구현되고, 프로세스 간 통신의 경우 일반적으로 사용자 정의 애플리케이션 계층의 프로토콜을 사용하여 구현된다.  
네트워크 환경에서 보통 기본적인 연결 확인 및 상태 모니터링에 자주 사용된다. 

![](https://media.geeksforgeeks.org/wp-content/uploads/20240318093301/Ping-Echo-Protocol-(1).webp)

### 3. UDP 기반 Heartbeat Protocol

UDP기반 노드간 통신을 원활하게 하기 위해, 일반적으로 하트비트 메세지가 포함된 가벼운 UDP 패킷을 주기적으로 전송한다.   
UDP 기반 하트비트 프로토콜은 낮은 지연 시간과 낮은 오버헤드가 요구되는 상황에 적합하다. 

![](https://media.geeksforgeeks.org/wp-content/uploads/20240313170325/UDP-based-Heartbeat-Protocol.webp)

### 4. TCP 기반 Heartbeat Protocol

TCP기반 노드들이 서로 메세지를 보낸다.

### 5. Apache ZooKeeper Heartbeats

Apache Zookeeper 하트비트는 분산 환경에서 세션관리와 리더 선출에 쓰인다.


###### Reference

![What are Heartbeat Messages?](https://www.geeksforgeeks.org/system-design/what-are-heartbeat-messages/)