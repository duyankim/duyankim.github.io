---
layout: post
author: "Yan"
catalog: true
subtitle: "HeadFirst Design Pattern을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 복합 패턴"
date: 2022-07-26 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 복합패턴
comments: true
---

# ✏️ 복합 패턴 

**복합패턴**: 반복적으로 생길 수 있는 일반적인 문제를 해결하는 용도로 2개 이상의 패턴을 결합해서 사용하는 것  

1. 오리가 우는 것을 구현한 Quackable 인터페이스를 만든다.
2. 거위도 Quackable 인터페이스를 구현해서 소리내고 싶다.
  - ✅ 어댑터 패턴으로 거위를 Quackable에 맞게 만들어줌
  - 거위 객체의 quack()을 호출하면 자동으로 honk() 메소드 실행
3. 꽥꽥 소리가 난 횟수를 세고 싶다
  - ✅ 데코레이터 패턴을 적용해 QuackCounter 데코레이터를 추가함
  - quack()이 호출된 횟수를 셀 수 있게 됨
  - quack()메소드 호출 자체는 그 데코레이터로 싸여 있는 Quackable객체가 처리됨
4. QuackCounter로 장식되지 않은 Quackable 객체가 있을 수도 있음을 우려
  - ✅ 추상 팩토리 패턴을 써서 팩토리가 객체를 만들도록 함
  - 오리 객체를 만들 때는 항상 팩토리에 요청을 하게 되니가, 팩토리가 데코레이터로 싸여있는 오리를 리턴하게 해서 문제를 해결
  - 데코레이터로 장식되지 않은 오리를 원한다면 다른 팩토리를 쓰면 된다.
5. 모든 오리, 거위, Quackable 객체를 무리 단위로 관리하고 싶다.
  - ✅ 컴포지트 패턴을 써서 오리를 모아서 오리 무리 단위로 관리하기로 함
  - 오리를 종별로 나눠서 관리할 수 있게 됨.
  - java.util의 반복자를 사용해서 반복자 패턴까지 적용함
6. Quackable에서 소리를 냈을 때 바로 연락받고 싶다.
  - ✅ 옵저버 패턴을 써서 Quackologist를 Quackable의 옵저버로 등록
  - Quackable에서 소리를 낼 때마다 연락을 받을 수 있음


###### reference

> HeadFirst Design Pattern   