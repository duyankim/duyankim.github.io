---
layout: post
author: "Yan"
catalog: true
subtitle: "실무에 적용되는 builder pattern"
header-img: "img/header/none4.jpg"
title: "빌더 패턴"
date: 2024-01-25 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 빌더 패턴
comments: true
---

# Builder pattern

Builder는 복잡한 객체들을 단계별로 생성할 수 있도록 하는 생성 디자인패턴

예를 들어,

`집`을 만들 때, `House` 클래스를 확장하고, 매개ㅐ변수의 모든 조합 (난방, 배관, 뒤뜰, 지붕 등)을 포함하는 자식 클래스들의 집합을 만들 수 있다. 그렇지만, 상당 수의 자식 클래스를 만들게 되고, 새로 추가할 때마다 계층구조가 복잡해질 것이다.

다른 방법으로는, 자식클래스를 늘리지 않고, `House`클래스에 `House` 객체를 제어하는 모든 가능한 매개변수를 포함한 거대한 생성자를 만들어서 사용한다면 대부분의 매개변수가 사용되지 않아 **생성자 호출 코드가 매우 못생겨질 것이다.**

해결책은 자신의 클래스에서 객체 생성 코드를 추출하여 Builder 객체로 이동한 Builder Pattern이다.

 

```java
Class HouseBuilder () {
	buildWalls();
	buildDoors();
	buildWindows();
	buildRoof();
	buildGarage();
	House getResult()
}
```

객체 생성을 일련의 단계들 (벽 건설하기, 문 건설하기 등등)로 정리하고, 객체를 생성하고 싶으면 위 단계들을 Builder 객체에 실행하면 된다.

- 모든 단계를 호출할 필요가 없고, 객체의 특정 설정을 제장하는 데 필요한 단계들만 호출하면 된다.
- 빌더는 제품이 생성되는 동안 다른 객체들이 제품에 접근하는 것을 허용하지 않는다.

일부 건축 단계들은 제품마다 다른 구현이 필요할 수 있는데, 건축 단계들의 집합을 표현하는 다른 여러 빌더 클래스들을 생성하여 사용할 수 있다.

- 건축 단계들을 호출하는 클라이언트 코드가 공통 인터페이스를 사용하여 빌더들과 상호작용할 수 있는 경우에만 작동한다.

### 디렉터 (관리자)

- 제품을 생성하는데 사용하는 빌더 단계들에 대한 일련을 호출을 디렉터라는 별도의 클래스로 추출할 수 있다.
- 렉터는 작동하는 제품을얻기 위하여 어떤 건축 단계들을 실행해야 하는지 알고 있다.
- 디렉터: 제작 단계들을 실행하는 **순서를 정의한다.**
- 빌더는: 제작 단계들에 대한 구현을 제공한다.
- 디렉터 클래스는 필수사항이 아니다. 그러나, 디렉터 클래스에 다양한 생성 루틴을 배치하여 프로그램 전체에서 재사용할 수 있으면 좋다.
- 디렉터 클래스는 클라이언트 코드에서 제품 생성의 세부 정보를 완전히 숨긴다. 클라이언트는 디렉터와 생성을 시행한 후 빌더로부터 결과를 얻기만 하면 된다.

![](https://refactoring.guru/images/patterns/diagrams/builder/structure.png?id=fe9e23559923ea0657aa5fe75efef333)

1. 빌더 인터페이스는 모든 유형의 빌더들에 공통적인 제품 생성 단계들을 선언한다.
2. 구상 빌더들은 생성 단계들의 다양한 구현을 제공한다. 구성 빌더들은 공통 인터페이스를 따르지 않는 제품들도 생성할 수 있다.
3. 제품들은 그 결과로 나온 객체들이다. 다른 빌더들에 의해 생성된 제품들은 같은 클래스 계층구조 또는 인터페이스에 속할 필요가 없다.
4. 디렉터 클래스는 생성 단계들을 호출하는 순서를 정의하므로 제품들의 특정 설정을 만들고 재사용할 수 있다.
5. 클라이언트는 빌더 객체들 중 하나를 디렉터와 연결해야 한다. 일반적으로 연결은 디렉터 생성자의 매개변수들을 통해 한 번만 수행되며, 그 후 디렉터는 모든 추가 생성에 해당 빌더 객체들을 사용한다.

## 빌더 패턴의 장점

- 객체들을 단계별로 생성하거나 생성 단계들을 연기하거나 재귀적으로 단계들을 실행할 수 있다.
- 제품들의 다양한 표현을 만들 때 같은 생성 코드를 재사용할 수 있다.
- *단일 책임 원칙*. 제품의 비즈니스 로직에서 복잡한 생성 코드를 고립시킬 수 있다.

## 빌더 패턴의 단점

- 패턴이 여러 개의 새 클래스들을 생성해야 하므로 코드의 전반적인 복잡성이 증가한다.

###### reference

> [builder pattern](https://refactoring.guru/ko/design-patterns/builder)