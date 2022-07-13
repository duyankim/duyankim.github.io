---
layout: post
author: "Yan"
catalog: true
subtitle: "HeadFirst Design Pattern을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 프록시 패턴"
date: 2022-07-13 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 프록시 패턴
comments: true
---

# ✏️ 프록시 패턴 Proxy Pattern

## 원격 프록시

- 원격 프록시의 역할 : 원격 객체의 **로컬 대변자**
  - 원격 객체 : 다른 주소 공간(다른 JVM의 Heap에서 살고 있는 객체)
  - 로컬 대변자 : 로컬 대변자의 어떤 메소드를 호출하면, 다른 원격 객체에 그 메소드 호출을 전달해 주는 객체
- 클라이언트 객체는 원격 객체의 메소드 호출을 하는 것 처럼 행동하지만, 실제로는 **로컬 힙에 들어있는 `프록시`객체의 메소드를 호출하고 있다.
- 네트워크 통신과 관련된 저수준 작업은 프록시 객체에서 처리해준다.

## 자바 RMI `Remote Method Invocation`

### RMI의 기능
- RMI는 클라이언트와 서비스 보조 객체를 만들어준다.
- 보조 객체에는 원격 서비스와 똑같은 메소드가 들어간다.
- 네트워킹 및 입출력 관련 코드를 직접 작성하지 않아도 된다.
- 클라이언트는 그냥 같은 로컬 JVM에 있는 메소드를 호출하듯이 원격 메소드를 호출할 수 있다.

### RMI 용어
- **스텁**`stub`: 클라이언트 보조 객체
- **스켈레톤**`skeleton`: 서비스 보조 객체
- `lookup` : 클라이언트가 원격 객체를 찾아서 접근할 때 쓸 수 있는 서비스



###### reference

> HeadFirst Design Pattern   