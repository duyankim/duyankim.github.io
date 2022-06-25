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

```java
public static synchronized Singleton getInstance() {
  if (uniqueInstance == null) {
    uniqueInstance = new Singleton();
    }
  return uniqueInstance;
}
```

- 해당 방식에서 아쉬운 점
  - 메소드를 동기화하면 성능이 100배정도 저하된다. 
  - Singleton 에 `synchronized` 메소드가 많을수록 멀티 스레드는 병목현상을 겪는다. 기껏 멀티 스레드를 사용하는데 Singleton 을 사용할 때는 싱글 스레드처럼 동작하는 문제가 발생한다는 뜻이다.  

#### synchronized

**동시성 프로그래밍** : 여러 테스크가 동시에 처리되도록 구현하는 것  

**동시성 보장**: 데이터 충돌과 같은 동시성 프로그래밍으로 발생되는 이슈를 피하는 방법  
- 동시성 보장 방식 3가지
1. synchronized
2. volatile
3. atomic


`synchronized` : 동기적으로 한 스레드가 메소드 사용을 끝내기 전까지 다른 스레드는 기다리게 만들어서, 여러개의 스레드가 해당 메소드를 동시에 실행하는 것을 방지한다.  

`synchronized`는 lock을 사용해 동기화를 시킨다

- 4가지 사용법이 있다.
1. **synchronized method** : 클래스의 인스턴스에 대하여 lock을 건다
2. **static synchronized method** : 우리가 일반적으로 생각하는 static의 성질을 갖는다. 인스턴스가 아닌 클래스 단위로 lock을 건다.
3. **synchronized block** : 인스턴스의 block단위로 lock을 건다. 
4. **static synchronized block** : static method안에 synchronized block을 지정할 수 있다. lock객체를 지정하여 특정 대상에만 lock을 걸 수 있다. 
- 인스턴스 단위: synchronized 키워드가 적용된 곳에서 전부 lock을 공유해서 쓴다. synchronized와 무관한 곳은 lock과 상관없다. 
- `method`와 `block`의 차이는 `method`는 해당 객체 전체에 lock을 걸고, `block`은 lock의 대상을 지정할 수 있으며 `block`으로 동기화가 적용되는 범위를 한정시킬 수 있다는 것이다.

### 2. 인스턴스가 필요할 때는 생성하지 않고 처음부터 만들기

애플리케이션에서 해당 클래스의 인스턴스를 생성하고 계속 사용하거나 인스턴스를 실행중에 수시로 만들고 관리하기가 성가시다면 이와 같은 방법으로 관리할 수 있다.

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

### 3. DCL을 써서 getInsance()에서 동기화되는 부분 줄이기

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

- 해당 방식에서 아쉬운 점
  - Thread A와 Thread B가 있다고 했을때, Thread A가 instance의 생성을 완료하기 전에 메모리 공간에 할당이 가능하기 때문에 Thread B가 할당된 것을 보고 instance를 사용하려고 하나 생성과정이 모두 끝난 상태가 아니기 때문에 오동작할 수 있다.

⚠️ 주의 : 자바 1.4 이전 버전에서는 voiatile 키워드를 써도 DCL에서 제대로 동기화가 안 되는 일이 많다.

#### volatile 

`volatile` : 동시성 프로그래밍에서 발생할 수 있는 문제 중 하나인 가시성 문제를 해결하기 위해 사용되는 키워드.  
- 가시성 문제 : 여러개의 스레드가 사용됨에 따라 `CPU Cache Memory`와 `RAM`의 데이터가 서로 일치하지 않아 생기는 문제
- 한 스레드가 변경된 값을 `CPU Cache Memory`에서 `RAM`에 데이터를 저장하기 전, 다른 스레드에서 `RAM`에서 해당 값을 읽어 변경되기 이전의 값을 처리하게 되는 상황을 "가시성이 보장되지 않는다" 라고 말한다. 
![cacheMemmory](https://user-images.githubusercontent.com/19832483/51120169-dc5fd480-1857-11e9-909e-4eb8201a2f44.png)
- `volatile` 키워드를 붙인 공유 자원은 `RAM`에 직접 읽고 쓰는 작업을 수행할 수 있도록 해준다. 가시성이 보장되어야하는 변수를 `CPU Cache Memory`에서 읽는 것이 아니라 `RAM`에서만 읽도록 보장하는 것이다.
![volatile](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbLWwSW%2FbtqRptqp9RK%2FrL4yGIT5y5iKQvGpk3zXTK%2Fimg.png)


### 4. ENUM 사용하기

Enum은 인스턴스가 여러 개 생기지 않도록 확실하게 보장해주고 복잡한 직렬화나 리플렉션 상황에서도 직렬화가 자동으로 지원된다는 이점이 있다.

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

### 5. LazyHolder

`synchronized`가 필요없고, 자바 버전도 상관 없는 방법이다. 

```java

public class Singleton {
  private Singleton() {}

  public static Singleton getInstance() {
    return LazyHolder.INSTANCE;
  }
  
  private static class LazyHolder {
    private static final Singleton INSTANCE = new Singleton();  
  }
}
```

- 객체가 필요할 때로 초기화를 미루는 `Lazy Initialization`
- Singleton 클래스에는 LazyHolder 클래스의 변수가 없기 때문에 Singleton 클래스 로딩 시 LazyHolder 클래스를 초기화하지 않는다.
- LazyHolder 클래스는 Singleton 클래스의 `getInstance()` 메서드에서 `LazyHolder.INSTANCE`를 참조하는 순간 Class가 로딩되며 초기화가 진행된다.
- Class를 로딩하고 초기화하는 시점은 thread-safe를 보장하기 때문에 volatile이나 synchronized 같은 키워드가 없어도 thread-safe 하면서 성능도 보장한다.

###### reference

> HeadFirst Design Pattern   
> [Multi Thread 환경에서의 올바른 Singleton](https://medium.com/@joongwon/multi-thread-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-%EC%98%AC%EB%B0%94%EB%A5%B8-singleton-578d9511fd42)  
> [[Java] 혼동되는 synchronized 동기화 정리](https://jgrammer.tistory.com/entry/Java-%ED%98%BC%EB%8F%99%EB%90%98%EB%8A%94-synchronized-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%A0%95%EB%A6%AC)  
> [자바 깊이 알기 / 자바의 동기화 방식](https://ecsimsw.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%9D%98-%EB%8F%99%EA%B8%B0%ED%99%94-%EB%B0%A9%EC%8B%9D-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B0%80%EC%8B%9C%EC%84%B1%EC%9D%B4%EB%9E%80-synchronized-volatile-atomic)  
> [자바의 동시성 #2 - 동시성 프로그래밍에서 발생할 수 있는 문제점과 volatile, synchrozied 키워드](https://badcandy.github.io/2019/01/14/concurrency-02/)