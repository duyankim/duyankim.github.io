---
layout: post
author: "Yan"
catalog: true
subtitle: "HeadFirst Design Pattern을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 싱글턴 패턴"
date: 2022-06-25 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 싱글턴패턴
comments: true
---

# ✏️ 싱글턴 패턴 Singleton Pattern

**싱글턴 패턴** : 클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공하는 패턴.  

## 🔂 싱글턴 패턴 구현법

1. 클래스의 하나뿐인 인스턴스를 저장하는 정적`static` 변수
2. private으로 생성자 선언. 해당 클래스에서만 클래스의 인스턴스를 만들 수 있음
3. public getInstance() 메소드는 클래스의 인스턴스를 만들어서 리턴함
4. 아직 인스턴스가 만들어지지 않았다면, 아예 인스턴스를 생성하지 않고 있는 `게으른 인스턴스 생성` 방식.

### 구현 코드
```java
public class Singleton {
  private static Singleton uniqueInstance;

  // 기타 인스턴스 변수

  private Singleton() {}

  public static Singleton getInstance() {
    if (uniqueInstance == null) {
      uniqueInstance = new Singleton();
    }
    return uniqueInstance;
  }

  // 기타 메소드
}
```

## 🔀 멀티스레딩 문제 해결하기

위의 싱글톤패턴 구현 코드를 사용한다면, 2개 이상의 스레드를 실행하면 인스턴스가 2개 이상으로 생성될 수 있다.  

자바 멀티 스레드 환경에서는 스레드끼리 static 영역과 heap 영역을 공유하므로 공유 자원에 대한 동기화 문제를 신경 써야 한다.



### 1. 동기화하기

`synchronized` : 동기적으로 한 스레드가 메소드 사용을 끝내기 전까지 다른 스레드는 기다리게 만들어서, 여러개의 스레드가 해당 메소드를 동시에 실행하는 것을 방지함.

```java
public static synchronized Singleton getInstance() {
  if (uniqueInstance == null) {
    uniqueInstance = new Singleton();
    }
  return uniqueInstance;
}
```

해당 방식에서 아쉬운 점 : 메소드를 동기화하면 성능이 100배정도 저하됨.  

### 2. 인스턴스가 필요할 때는 생성하지 않고 처음부터 만들기

애플리케이션에서 해당 클래스의 인스턴스를 생성하고 계속 사용하거나 인스턴스를 실행중에 수시로 만들고 관리하기가 성가시다면 이와 같은 방법으로 관리할 수 잇다.

```java
public class Singleton {
  private static Singleton uniqueInstance = new Singleton();
  // 인스턴스를 미리 생성해두면 스레드를 써도 별 문제가 없다.

  private Singleton();

  public static Singleton getInstance() {
    return uniqueInstance;
  }
}
```

이런 방법으로 클래스가 로딩될 때 JVM에서 Singleton의 하나뿐인 인스턴스를 생성하면, JVM에서 하나뿐인 인스턴스를 생성하기 전까지 어떤 스레드도 uniqueInstance 정적 변수에 접근할 수 없다.

### 3. DCL을 써서 getInsance()에서 동기화되는 부분을 줄인다.

**DCL** : Double Checked Locking  
- 인스턴스가 생성되어 있는지 확인한 다음 생성되어 있지 않았을 때만 동기화할 수 있다.
- 처음에만 동기화하고  나중에는 동기화하지 않아도 된다.

```java
public class Singleton {
  private volatile static Sigleton uniqueInstance;

  private Singleton() {}

  public static Singleton getInstance() {
    if (uniqueInstance == null) {
      // 인스턴스가 없으면 동기화 블록으로 들어간다.
      synchronized (Singleton.class) {
        // 처음에만 동기화된다.
        if (uniqueInstance == null) {
          // 변수가 null인지 확인한 다음 인스턴스를 생성한다.
          uniqueInstance = new Singleton();
        }
      }
    }
    return uniqueInstance;
  }
}
```

⚠️ 주의 : 자바 1.4 이전 버전에서는 voiatile 키워드를 써도 DCL에서 제대로 동기화가 안 되는 일이 많다.

### 4. ENUM 사용하기

동기화 문제, 클래스 로딩 문제, 리플렉션, 직렬화와 역질렬화 문제는 enum으로 싱글턴을 생성해서 해결할 수 있다.

```java
public enum Singleton {
  UNIQUE_INSTANCE;
  //기타 필요한 필드
}
```

```java
public class SingletonClient {
  public static void main(String[] args) {
    Singleton singleton = Singleton.UNIQUE_INSTANCE;
    //여기서 싱글턴 사용
  }
}
```

싱글턴이 필요할 때면 바로 ENUM을 쓰면 된다.

###### reference

> HeadFirst Design Pattern  
