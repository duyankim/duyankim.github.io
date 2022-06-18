---
layout: post
author: "Yan"
catalog: true
subtitle: "HeadFirst Design Pattern을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 04. 팩토리 패턴"
date: 2022-06-14 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 팩토리패턴
comments: true
---

# ✏️ 04장 팩토리 패턴 Factory Pattern

> 'new'연산자를 사용하면 구상 클래스의 인스턴스가 만들어진다.   
> 구상 클래스를 바탕으로 코딩하면 나중에 수정해야 할 가능성이 커지고 유연성이 떨어진다.   
> `다형성`을 활용하면 시스템에서 일어날 수 있는 여러 변화에 대처할 수 있다. 구현해야 하는 클래스에 implements해서 사용할 수 있기 때문이다.  
> 우리는 `인터페이스`에 맞춰서 코딩하여 `확장에는 열려있고, 변경에는 닫혀있는` 코드를 쓰자.


## 팩토리 메소드 패턴  

팩토리 메소드 패턴은 객체를 생성할 때 필요한 인터페이스를 만든다.  
어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정한다.  
팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에 맡기게 된다.  
![factory method pattern](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a3/FactoryMethod.svg/600px-FactoryMethod.svg.png)

- 팩토리 메소드 패턴으로 구상 형식 인스턴스를 만드는 작업을 캡슐화할 수 있다.
- Creator 추상 클래스에서 객체를 만드는 메소드(팩토리 메소드용 인터페이스)를 제공한다.
- Creator 추상 클래스에 구현되어 있는 다른 메소드는 팩토리 메소드에 의해 생산된 제품으로 필요한 작업을 처리한다.
- 실제 팩토리 메소드를 구현하고 제품(객체 인스턴스)을 만드는 일은 서브클래스에서만 할 수 있다.
- 사용하는 서브클래스에 따라 생산되는 객체 인스턴스가 결정된다.


## 🍕 1. 객체 생성 부분을 캡슐화하여 객체 생성 팩토리 만들기

> 피자 가게 예제

```java
Pizza orderPizza(String type) {
  Pizza pizza;

  if (type.equals("cheese")) {
    pizza = new CheesePizza();
  } else if (type.equals("clam")) {
    pizza = new ClamPizza();
  } else if (type.equals("clam")) {
    pizza = new ClamPizza();
  } 

  pizza.prepare();
  pizza.bake();
  pizza.cut();
  pizza.box();
  return pizza;
}
```

- 인스턴스를 만드는 구상 클래스를 선택하는 부분은 피자 종류가 바뀔 때마다 코드를 계속 고쳐야한다. 그 부분만 따로 떼어서 캡슐화해보자

### 팩토리 

🏭 **팩토리**: 객체 생성을 처리하는 클래스
```java
public class SimplePizzaFactory {

  public Pizza createPizza(String type) {
    Pizza pizza = null;

    if (type.equals("cheese")) {
      pizza = new CheesePizza();
    } else if (type.equals("clam")) {
      pizza = new ClamPizza();
    } else if (type.equals("clam")) {
      pizza = new ClamPizza();
    } 
    return pizza;
  }
}
```
다양한 팩토리를 만들기 위해서, `SimplePizzaFactory`를 삭제하고  
3가지 서로 다른 팩토리 (`NYPizzaFactory`, `ChicagePizzaFactory`, `CaliforniaPizzaFactory`)를 만들어서  
`PizzaStore`에서 적당한 팩토리를 사용하도록 변경할 수 있다.

🏭 클라이언트 코드
```java
public class PizzaStore {
  SimplePizzaFactory factory;

  public PizzaStore(SimplePizzaFactory factory) {
    this.factory = factory;
  }

  public Pizza orderPizza(String type) {
    Pizza pizza;

    pizza = factory.createPizza(type);

    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    return pizza;
  }
}
```


## 🍕 2. 서브클래스를 활용한 피자 가게 프레임워크로 팩토리 메소드 패턴 알아보기

피자 만드는 일 자체는 전부 `PizzaStore` 클래스에서 진행하면서도, 각 팩토리의 스타일의 살리려면,  
`PizzaStore`의 `createPizza()` 메소드를 **추상메소드**로 선언하고, `PizzaStore`의 **서브클래스**를 만든다

```java
public abstract class PizzaStore {

  protected abstract Pizza createPizza(String type);
  // abstract Product factoryMethod(String type)으로 볼 수 있다.

  public Pizza orderPizza(String type) {
    Pizza pizza;

    pizza = createPizza(type);

    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    return pizza;
  }
}
```

- 서브클래스인 `NYPizzaFactory`, `ChicagePizzaFactory`, `CaliforniaPizzaFactory`에서 추상메소드인 `createPizza()`를 구현하여 사용하면 된다.
- `orderPizza()`는 서브클래스가 아닌 추상 클래스에서 정의한다. 따라서 이 메소드는 실제로 어떤 서브클래스에서 코드를 실행하는지 알 수가 없다.
- 피자의 종류는 어떤 서브클래스를 선택했느냐에 따라 결정된다.

#### `abstract Product factoryMethod(String type)`

- **abstract** : `factoryMehtod()`를 추상 메소드로 선언해서 서브클래스가 객체 생성을 책임지도록 한다. 
- **Product** : 측정 객체를 리턴한다. 그 객체는 보통 슈퍼클래스가 정의한 메소드 내에서 쓰인다.

### 서브클래스 예시

#### 생산자 `Creator` 서브클래스

```java
public class NYPizzaStore extends PizzaStore {

  Pizza createPizza(String item) {
    if (item.equals("cheese")) {
      return new NYStyleCheesePizza();
    } else if (item.equals("veggie")) {
      return new NYStyleVeggiePizza();
    } else if (item.equals("clam")) {
      return new NYStyleClamPizza();
    } else if (item.equals("pepperoni")) {
      return new NYStylePepperoniPizza();
    } else return null;
  }
}
```
- **PizzaStore** : 추상 생산자 클래스로, 나중에 서브클래스에서 `Product`를 생산하려고 구현하는 `abstract` 팩토리 메소드를 정의한다.
- **NYPizzaStore**에는 뉴욕 스타일 피자를 만드는 모든 방법이 **캡슐화**되어 있다.
- `NYPizzaStore`, `ChicagoPizzaStore`와 같이 제품을 생산하는 클래스를 구상 생산자`concrete creator`라고 부른다.


#### 제품 `Product` 서브클래스

```java
public class NYStyleCheezePizza extends Pizza {

  public NYStyleCheesePizza() {
    name = "뉴욕 스타일 치즈 피자";
    dough = "씬 크러스트 도우";
    sauce = "마리나라 소스";

    toppings.add("잘게 썬 레지아노 치즈");
  }
}
```
- **Pizza** : 추상 제품 클래스로, 팩토리가 생산하는 제품이다. `PizzaStore`는 `Pizza`를 만든다.
- `PizzaStore`는 결국 `NYStyleCheezePizza`같은 제품 클래스를 만든다.

###### reference

> HeadFirst Design Pattern  
> [위키백과 팩토리 메서드 패턴](https://ko.wikipedia.org/wiki/%ED%8C%A9%ED%86%A0%EB%A6%AC_%EB%A9%94%EC%84%9C%EB%93%9C_%ED%8C%A8%ED%84%B4)