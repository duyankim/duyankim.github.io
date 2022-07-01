---
layout: post
author: "Yan"
catalog: true
subtitle: "HeadFirst Design Pattern을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 템플릿 메소드 패턴"
date: 2022-07-01 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 템플릿 메소드 패턴
comments: true
---

# ✏️ 템플릿 메소드 패턴 Template Method Pattern

**템플릿 메소드 패턴** : 알고리즘의 골격을 정의한다. 알고리즘의 일부 단계를 서브클래스에서 구현할 수 있으며, 알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브클래스에서 재정의할 수도 있다.

- 알고리즘의 틀(템플릿)을 만들고, 그 클래스에서 알고리즘을 독점하여 작업을 처리한다.
- 템플릿의 서브클래스에서 코드를 재사용할 수 잇다.
- 알고리즘이 한 군데 모여 잇으므로 한 부분만 고치면 된다.
- 템플릿 클래스에 알고리즘 지식이 집중되 있으며 일부 구현만 서브클래스에 의존한다.

###### reference

> HeadFirst Design Pattern   