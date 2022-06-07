---
layout: post
author: "Yan"
catalog: true
subtitle: "<HeadFirst Design Pattern>을 읽고"
header-img: "img/header/none4.jpg"
title: "01장 - 디자인 패턴의 세계로 떠냐기 '전략패턴'"
date: 2022-06-08 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 전략패턴
comments: true
---

> 오리 시뮬레이션 게임을 만들고 있다고 가정한다.

# 1. `Duck` 슈퍼 클래스를  상속받은 다양한 유형의 오리 만들기

```java
public class Duck {
  public void quack() {
    // 모든 오리가 소리를 낼 수 있다.
  }

  public void swim() {
    // 모든 오리가 수영할 수 있다.
  }

  public abstract void display(); // 모든 오리의 모양이 다르기 때문에 추상클래스로 구성

  // 기타 등등의 오리 관련 메소드
}

public class MallardDuck extends Duck {
  @Override
  public void display() {
    // 모양을 나타냄
  }
}

public class RedheadDuck extends Duck {
  @Override
  public void display() {
    // 모양을 나타냄
  }
}
```

### 추상 메소드 

- 자식 클래스에서 반드시 오버라이딩해야만 사용할 수 있는 메소드를 의미
- 모듈처럼 중복되는 부분이나 공통적인 부분은 미리 다 만들어진 것을 사용하고, 이를 받아 사용하는 쪽에서는 자신에게 필요한 부분만을 재정의하여 사용함으로써 생산성이 향상되고 배포 등이 쉬워짐
- 선언부만 존재. 구현부는 작성되어 있지 않음

####  자바에서 추상 메소드를 선언하여 사용하는 목적
- 추상 메소드가 포함된 클래스를 상속받는 자식 클래스가 반드시 추상 메소드를 구현하도록 하기 위함
- 만약 일반 메소드로 구현한다면 사용자에 따라 해당 메소드를 구현할 수도 있고, 안 할 수도 있다.
- 하지만 추상 메소드가 포함된 추상 클래스를 상속받은 모든 자식 클래스는 추상 메소드를 구현해야만 인스턴스를 생성할 수 있으므로, 반드시 구현하게 됩니다.


###### reference

> HeadFirst Design Pattern  
> [추상 클래스](http://www.tcpschool.com/java/java_polymorphism_abstract)
