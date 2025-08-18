---
layout: post
author: "Yan"
catalog: true
subtitle: "고가용성 시스템을 설계해보자"
header-img: "img/header/spring.jpg"
title: "HA Acitve-acitve, Active Passive"
date: 2025-08-18 11:40:08 +0000
categories:
  - Arcitecture
tags:
  - HA
comments: true
---

# Active-Active vs. Active-Passive HA
**고가용성(HA)**을 구현하는 방식에는 크게 *액티브-액티브(Active-Active)*와 *액티브-패시브(Active-Passive)* 구조가 있다. 

- 액티브-액티브 구성은 여러 노드가 동시에 서비스 트래픽을 처리하여 부하를 분산하고, 한 노드가 장애가 나도 다른 노드들이 계속 서비스를 제공하는 방식이다.
- 반면 액티브-패시브 구성에서는 한 노드(주 노드)만 업무를 처리하고 다른 노드들은 대기 상태에 있다가 주 노드 장애 시에만 승격되어 서비스를 이어받는다.

- 액티브-패시브는 장애 복구 시 일정 지연이 발생할 수 있지만 구조가 단순하며, 액티브-액티브는 실시간 부하 분산과 무중단 성능이 뛰어난 대신 구성 복잡성이 높다. 
- 요구사항(예: RPO/RTO 목표)에 따라 두 방식 중 적절한 HA 구성을 선택해야 한다.


## Active-Passive Architecture

- 액티브-패시브는 Standby 또는 Failover 아키텍처라고도 불린다. 동일한 구성의 여러 서버가 떠 있지만, 그 중 하나만 운영중이고, 만약 가동중인 시스템이 실패했을때 passive가 major로 승격하면서 운영하게 된다. 

### 주요한 특징

- Primary (Active) Server와 Standby (Passive) Server가 있다.
- Heartbeat mechanism이 필요하다. 무응답이나 시스템 문제를 감지할 수 있는 active server에 대한 hearbeat 체크를 할 수 있는 시스템이 필요하다. 


