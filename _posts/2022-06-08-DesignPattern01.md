---
title: Gof Design Pattern - 전략패턴
excerpt: HeadFirst Design Pattern을 읽고
date: "2022-06-08 23:15:08 +0000"
categories:
  - Architecture
  - Design Pattern
tags:
  - 디자인패턴
  - Gof
  - 전략패턴
author: Duyan Kim
toc: true
toc_sticky: true
header:
  teaser: /img/header/none4.jpg
---
# ✏️ 전략패턴 Strategy Pattern
**전략패턴**: 알고리즘군을 정의하고 캡슐화해서 각각의 알고리즘군을 수정해서 쓸 수 있게 해준다. 클라이언트로부터 알고리즘을 분리해서 독립적으로 변경할 수 있게 된다.

> 예제: 오리 시뮬레이션 게임 만들기  

## 🐥 1. `Duck` 슈퍼 클래스를  상속받은 다양한 유형의 오리 만들기

```java
public abstract class Duck {
  public void quack() {
    // 모든 오리가 소리를 낼 수 있다.
  }

  public void swim() {
    // 모든 오리가 수영할 수 있다.
  }

  public abstract void display(); 
  // 모든 오리의 모양이 다르기 때문에 추상클래스로 구성

  // 기타 등등의 오리 관련 메소드
}
```

```java
public class MallardDuck extends Duck {
  @Override
  public void display() {
    // 모양을 나타냄
  }
}
```

```java
public class RedheadDuck extends Duck {
  @Override
  public void display() {
    // 모양을 나타냄
  }
}
```

### 추상 클래스
- 하나 이상의 추상 메소드를 포함하는 클래스다.
- 객체지향 프로그래밍에서 중요한 특징인 다형성을 가지는 메소드의 집합을 정의할 수 있도록 해준다.

#### 자바에서 추상 클래스를 선언하여 사용하는 목적
- 공통된 필드와 메소드를 통일하기 위함이다.
- 규격에 맞는 실체 클래스를 구현하기 위함이다.
- 실체 클래스를 구현할 때 시간 절약을 할 수 있다.
### 추상 메소드 
- 자식 클래스에서 반드시 오버라이딩해야만 사용할 수 있는 메소드를 의미한다.
- 모듈처럼 중복되는 부분이나 공통적인 부분은 미리 다 만들어진 것을 사용하고, 이를 받아 사용하는 쪽에서는 자신에게 필요한 부분만을 재정의하여 사용함으로써 생산성이 향상되고 배포 등이 쉬워진다.
- 선언부만 존재. 구현부는 작성되어 있지 않다.

####  자바에서 추상 메소드를 선언하여 사용하는 목적
- 추상 메소드가 포함된 클래스를 상속받는 **자식 클래스가 반드시 추상 메소드를 구현하도록 하기 위함이다**
- 만약 일반 메소드로 구현한다면 사용자에 따라 해당 메소드를 구현할 수도 있고, 안 할 수도 있다.
- 하지만 추상 메소드가 포함된 추상 클래스를 상속받은 모든 자식 클래스는 추상 메소드를 구현해야만 인스턴스를 생성할 수 있다.

## 🐥 2. 상속을 생각하여 오리에게 fly() 메소드 추가하기

아래와 같이 Duck 슈퍼 클래스에 fly() 메소드를 넣으면 코드를 재사용할 수 있지만, 유지보수에 용이하지 않다.

```java
public abstract class Duck {
  public void quack() { }

  public void swim() { }

  public abstract void display(); 

  public void fly() { }

  // 기타 등등의 오리 관련 메소드
}
```

만약, 날지 못하는 고무 오리가 Duck 슈퍼 클래스를 상속받으면, fly() 메소드가 아무것도 하지 않도록 오버라이드 해야 한다.  

### Duck의 행동을 상속하면 생기는 단점들
- 서브클래스에서 코드가 중복된다.
- 실행 시에 특징을 바꾸기 힘들다.
- 모든 오리의 행동을 알기 힘들다.
- 코드를 변경했을 때 다른 오리들에게 원치 않은 영향을 끼칠 수 있다.

## 🐥 3. 디자인 원칙 적용하기

### 📘 디자인 원칙 1
> 애플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분과 분리한다.  

💊 달라지는 부분을 찾아서 나머지 코드에 영향을 주지 않도록 **캡슐화**해야 한다.

#### 캡슐화의 장점
바뀌는 부분만 따로 뽑아서 캡슐화하면 나중에 바뀌지 않는 부분에는 영향을 미치지 않고, 그 부분만 고치거나 확장할 수 있다.  
코드를 변경하는 과정에서 의도치 않게 발생하는 일을 줄이면서 시스템의 유연성을 확장시킬 수 있다.

##### 바뀌는 부분과 바뀌지 않는 부분 골라내기
**바뀌지 않는 부분**  

```java
public abstract class Duck {
  public void swim() { }

  public abstract void display(); 
}
```

**바뀌는 부분**  
- 꽥꽥 거리는 행동 `quack()`
- 나는 행동 `fly()`  
Duck 클래스와는 완전히 별개로 2개의 클래스 집합을 만들어야 한다. 각 클래스 집합에는 각각의 행동을 구현한 것을 전부 집어넣는다. 

### 📘 디자인 원칙 2
> 구현보다는 인터페이스에 맞춰서 프로그래밍한다.  

#### 💊 적용 방법
- 각 행동은 인터페이스로 표현하고, 인터페이스를 사용해서 행동을 구현한다.
- 특정 행동만을 목적으로 하는 클래스의 집합을 만든다. 
- 행동`behavior`인터페이스는 행동 클래스에서 구현한다. 
 
#### 💊 기대 효과
- Duck 클래스에서 행동을 구체적으로 구현할 필요가 없다.
- 이전의 방식은 특정 구현에 의존했기 때문에 행동을 변경할 여지가 없었는데, 변경 후에는 행동을 구현한 클래스에 Duck 서브 클래스가 국한되지 않는다. (Duck 클래스를 전혀 건드리지 않고도 새로운 행동을 추가할 수 있다)

#### 💊 적용한 코드
**나는 행동**
```java
public interface FlyBehavior {
  public void fly();
}
```
날 수 있는 클래스는 무조건 FlyBehavior 인터페이스를 구현한다. 날 수 있는 클래스를 새로 만들 때는 무조건 fly메소드를 구현해야 한다.

```java
public class FlyWithWings implements FlyBehavior {
  @Override
  public void fly() {
    // 나는 방법 구현
  }
}
```

```java
public class FlyNoWay implements FlyBehavior {
  @Override
  public void fly() {
    // 날 수 없음. 아무것도 하지 않음.
  }
}
```

------
**소리내는 행동**
```java
public interface QuackBehavior {
  public void quack();
}
```
소리낼 수 있는 클래스는 무조건 QuackBehavior 인터페이스를 구현한다. 

```java
public class Quack implements QuackBehavior {
  @Override
  public void quack() {
    // 꽥꽥 소리
  }
}
```

```java
public class Squeak implements QuackBehavior {
  @Override
  public void quack() {
    // 삑삑 소리
  }
}
```

```java
public class MuteQuack implements QuackBehavior {
  @Override
  public void quack() {
    // 아무 소리도 내지 않음
  }
}
```

#### 인터페이스에 맞춰서 프로그래밍한다는 것의 의미

- 실제 실행시에 쓰이는 객체가 코드에 고정되지 않도록 상위형식(subtype)에 맞춰 프로그래밍해서 다형성을 활용해야 한다.
- 변수를 선언할 때 보통 추상 클래스나 인터페이스 같은 상위 형식으로 선언해야 한다.
  - 객체를 변수에 대입할 때 상위 형식을 구체적으로 구현한 형식이라면 어떤 객체든 넣을 수 있기 때문에, 변수를 선언하는 클래스에서 실제 객체의 형식을 몰라도 된다.

## 🐥 4.오리 행동 구현하기

나는 행동과 꽥꽥거리는 행동을 Duck 클래스에서 정의한 메소드를 써서 구현하지 않고, **다른 클래스에 위임한다**.  

```java
public abstract class Duck {

  FlyBehavior flyBehavior;
  QuackBehavior quackBehavior;

  public Duck() { };

  public abstract void display(); 

  public void swim() {
    System.out.println("모든 오리는 물에 뜬다");
  }

  public void performQuack() {
    quackBehavior.quack();
  }

  public void performFly() {
    flyBehavior.fly();
  }
}
```
행동을 직접 처리하는 대신, `quackBehavior`, `flyBehavior`로 참조되는 객체에 그 행동을 위임한다.

```java
public class MallardDuck extends Duck {
  public MallardDuck() {
    quackBehavior = new Quack();
    flyBehavior = new FlyWithWings();
  }
  
  public void display() {
    System.out.println("저는 물오리입니다.");
  }
}
```

MallardDuck 인스턴스가 만들어질 때  
- 생성자는 Duck으로부터 상속받은 `quackBehavior` 인스턴스 변수에 `Quack`(`QuackBehavior`를 구현한 구상 클래스)형식의 새로운 인스턴스를 대입한다.
- 생성자는 Duck으로부터 상속받은 `flyBehavior` 인스턴스 변수에 `FlyWithWings`(`flyBehavior`를 구현한 구상 클래스)형식의 새로운 인스턴스를 대입한다.

### 오리 코드 테스트하기 

```java
public class MiniDuckSimulator {
  public static void main(String[] args) {
    Duck mallard = new MallardDuck();
    mallard.performQuack();
    mallard.performFly();
  }
}
```

## 🐥 5.동적으로 행동 지정하기
Duck 클래스에 setter method를 넣어준다.

```java
public abstract class Duck {

  FlyBehavior flyBehavior;
  QuackBehavior quackBehavior;

  public Duck() { };

  public abstract void display(); 

  public void swim() {
    System.out.println("모든 오리는 물에 뜬다");
  }

  public void performQuack() {
    quackBehavior.quack();
  }

  public void performFly() {
    flyBehavior.fly();
  }

  public void setFlyBehavior(FlyBehavior fb) {
    flyBehavior = fb;
  }

  public void setQuackBehavior(QuackBehavior qb) {
    quackBehavior = qb;
  }
}
```

## 🐥 6.두 클래스를 합치기
### 📘 디자인 원칙 3
> 상속보다는 구성을 활용한다.  

- 클라이언트에서는 나는 행동과 꽥꽥거리는 행동을 캡슐화된 알고리즘으로 구현한다.
- 각 오리에는 `FlyBehavior`와 `QuackBehavior`가 있고, 각각 나는 행동과 꽥꽥거리는 행동을 위임받는다. 이런 식으로 두 클래스를 합치는 것을 구성`composition`을 이용한다고 부른다.
- 구성을 활용해서 시스템을 만들면 유연성을 크게 향상시킬 수 있다.
- 알고리즘군을 별도의 클래스 집합으로 캡슐화할 수 있으며, 구성 요소로 사용하는 객체에서 올바른 인터페이스를 구현하기만 하면 실행시에 행동을 바꿀 수도 있다.

###### reference

> HeadFirst Design Pattern  
> [추상 클래스](https://limkydev.tistory.com/188)  
> [추상 메소드](http://www.tcpschool.com/java/java_polymorphism_abstract)  
