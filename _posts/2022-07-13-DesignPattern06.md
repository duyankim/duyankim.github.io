---
title: Gof Design Pattern - 프록시 패턴
excerpt: HeadFirst Design Pattern을 읽고
date: "2022-07-13 23:15:08 +0000"
categories:
  - Architecture
  - Design Pattern
tags:
  - 디자인패턴
  - Gof
  - 프록시 패턴
author: Duyan Kim
toc: true
toc_sticky: true
header:
  teaser: /img/header/none4.jpg
---
# ✏️ 프록시 패턴 Proxy Pattern

**프록시 패턴** : 특정 객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체) 제공  

- 프록시 패턴을 사용하면 원격 객체, 생성하기 힘든 객체, 보안이 중요한 객체와 같은 다른 객체로의 접근을 제어하는 대리인 객체를 만들 수 있다.
- `원격 프록시`를 써서 원격 객체로의 접근을 제어할 수 있다.
- `가상 프록시`를 써서 생성하기 힘든 자원으로의 접근을 제어할 수 있다.
- `보호 프록시`를 써서 접근 권한이 필요한 자원으로의 접근을 제어할 수 있다.

![proxy pattern](https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Proxy_pattern_diagram.svg/800px-Proxy_pattern_diagram.svg.png)
- `Subject`: `Proxy`와 `RealSubject` 모두 `Subject`인터페이스를 구현해 어떤 클라이언트에서든 프록시를 `Subject`와 똑같은 식으로 다룰 수 있다.
- `RealSubject`: 진짜 작업을 대부분 처리하는 객체. s
- `Proxy`: 진짜 작업을 처리하는 객체의 레퍼런스가 들어있다. `RealSubject`의 인스턴스를 생성하거나, 그 객체의 생성 과정에 관여한느 경우가 많다. `RealSubject`로의 접근을 제어한다.

## 👽 원격 프록시

- 원격 프록시의 역할 : 원격 객체의 **로컬 대변자**
  - **원격 객체** : 다른 주소 공간(다른 JVM의 Heap에서 살고 있는 객체)
  - **로컬 대변자** : 로컬 대변자의 어떤 메소드를 호출하면, 다른 원격 객체에 그 메소드 호출을 전달해 주는 객체
- 클라이언트 객체는 원격 객체의 메소드 호출을 하는 것 처럼 행동하지만, 실제로는 **로컬 힙에 들어있는 `프록시`객체의 메소드**를 호출하고 있다.
- 네트워크 통신과 관련된 저수준 작업은 프록시 객체에서 처리해준다.

### 👽 원격 메소드
![remote method](https://miro.medium.com/max/975/1*g8neQevs0ESl7swCpaNnxA.png)
- **클라이언트 객체** : 진짜 서비스의 메소드를 호출한다고 생각한다.
- **클라이언트 보조 객체** = proxy. 원격 객체로 요청을 전달한다.
- **서비스 보조 객체** : Socket 연결로 클라이언트 보조 객체로부터 요청을 받는다. 받은 요청을 해석해서 진짜 서비스에 있는 메소드를 호출한다.
- **서비스 객체** : 실제로 작업을 처리하는 메소드가 들어있다. 

## 🛸 자바 RMI `Remote Method Invocation`

### 🛸 RMI의 기능
- RMI는 클라이언트와 서비스 보조 객체를 만들어준다.
- 보조 객체에는 원격 서비스와 똑같은 메소드가 들어간다.
- 네트워킹 및 입출력 관련 코드를 직접 작성하지 않아도 된다.
- 클라이언트는 그냥 같은 로컬 JVM에 있는 메소드를 호출하듯이 원격 메소드를 호출할 수 있다.

### 🛸 RMI 구조
- **스텁**`stub`: 클라이언트 보조 객체
- **스켈레톤**`skeleton`: 서비스 보조 객체
- `lookup` : 클라이언트가 원격 객체를 찾아서 접근할 때 쓸 수 있는 서비스
![RMI](https://t1.daumcdn.net/cfile/tistory/2704234F58ABB10B1D)

### 👽 원격 서비스 만드는 방법

#### 1. 원격 인터페이스 만들기
클라이언트가 원격으로 호출할 메소드 정의. 스텁과 실제 서비스에 이 인터페이스를 구현해야 한다.  

1. `java.rmi.Remote`를 확장하여 사용한다. (인터페이스를 확장해서 다른 인터페이스를 만들 수 있다.)
  ```java
  public interface MyRemote extends Remote
  ```
2. 모든 메소드가 `RemoteException`을 던지도록 선언한다. 모든 원격 메소드 호출은 위험이 따르는 것으로 간주하여 클라이언트에서 예외 상황 발생을 대비한다.
3. 원격 메소드의 인자와 리턴값은 반드시 primitive 또는 Serializable 형식으로 선언한다.

#### 2. 서비스 구현 클래스 만들기
실제 작업을 처리하는 클래스. 클라이언트에서 이 객체에 있는 메소드를 호출. 실제 작업을 처리하는 메소드가 구현되어 있다.  

1. 서비스 클래스에 원격 인터페이스를 구현한다.
  ```java
  public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {
    // overide한 메소드
  }
  ```
2. `UnicastRemoteObject`을 확장한다. 원격 서비스 객체 역할을 하려면 원격 객체 기능을 추가해야 되는데, 이는 `java.rmi.server`의 `UnicastRemoteObject`를 확장해서 슈퍼클래스에서 제공하는 기능으로 처리할 수 있다.
  ```java
  public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {
    private static final long serialVersionUID = 1L;
    // overide한 메소드
  }
  ```
3. `RemoteException`을 선언하는 생성자를 구현한다.
  ```java
  public MyRemoteImpl() throws RemoteException();
  ```
4. 서비스를 RMI 레지스트리에 등록한다. 원격 서비스를 원격 클라이언트에서 쓸 수 있게 인스턴스를 만든 다음 RMI 레지스트리를 등록한다.
  ```java
  try {
    MyRemote service = new MyRemoteImpl();
    Naming.rebind("RemoteHello", service);  // 여기서 등록한 이름으로 rmiRegistry에 결합
  } catch (Exception ex) {...}
  ```

##### 👽 서버에서 쓰는 코드 샘플

###### 원격 인터페이스 

```java
import java.rmi.*;

public interface MyRemote extends Remote {
  public String sayHello() throws RemoteException;
}
```

###### 원격 서비스를 구현한 클래스

```java
import java.rmi.*;
import java.rmi.server.*;

public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {
  private static final long serialVersionUID = 1L;

  public String sayHello() {
    return "Server says, 'Hey'";
  }

  public MyRemoteImpl() throws RemoteException{ }

  public static void main (String[] args) {
    try {
      MyRemote service = new MyRemoteImpl();
      Naming.rebind("RemoteHello", service);
    } catch (Exception ex) {
      ex.printStackTrace();
    }
  }
}
```


#### 3. RMI registry 실행하기
다른 터미멀 창에서 `%rmiregistry`실행한다. 이 레지스트리로부터 프록시(스텁, 클라이언트 보조 객체)를 받아간다.  

#### 4. 원격 서비스 실행하기
다른 터미널을 열고 서비스 객체를 실행해야 한다. 서비스를 구현한 클래스에서 서비스의 인스턴스를 만들고 그 인스턴스를 RMI 레지스트리에 등록한다. 스텁과 스켈레톤은 보이지 않는 곳에서 동적으로 만든다.

#### 👽 스텁 객체를 룩업하는 클라이언트

레지스트리에서 리턴된 객체는 Object 형식이니까 반드시 캐스팅을 해야한다.

1. 클라이언트에서 RMI 레지스트리 룩업
  ```java
  MyRemote service = (MyRemote) Naming.lookup("rmi://127.0.0.1/RemoteHello");
  ```
2. RMI 레지스트리에서 스텁 객체를 리턴. 스텁 객체는 자동으로 역질렬화되어서 전달됨. 이때 스텁 클래스는 반드시 클라이언트에만 있어야 한다.
3. 클라이언트는 스텁의 서비스 메소드를 호출한다. 스텁이 진짜 서비스 객체라고 생각한다.

###### 클라이언트 샘플

```java
import java.rmi.*;

public class MyRemoteClient {
  
  public static void main (Sring[] args) {
    new MyRemoteClient().go();
  }

  public void go() {

    try {
      MyRemote service = (MyRemote) Naming.lookup("rmi://127.0.0.1/RemoteHello");
      String s = servcie.sayHello();
      System.out.println(s);
    } catch (Exception ex) {
      ex.printStackTrace();
    }
  }
}
```

## 🛰 가상 프록시

- 생성하는 데 많은 비용이 드는 객체를 대신한다.
- 진짜 객체가 필요한 상황이 오기 전까지 객체의 생성을 미루는 기능을 제공한다.
- 객체 생성이 끝나면 `RealSubject`에 직접 요청을 전달한다.

## 🪐 보호 프록시

- **동적 프록시** : `java.lang.reflect`의 프록시 기능을 이용하여 즉석에서 하나 이상의 인터페이스를 구현하고, 지정한 클래스에 메소드 호출을 전달하는 프록시 클래스가 실행 중에 생성되는 것
- **보호 프록시** : 접근 권한을 바탕으로 객체로의 접근을 제어하는 프록시
  - `InvocationHandler`에 프록시 클래스에 넣을 수 없는 *Proxy 클래스에게 무슨 일을 해야 하는지 알려 줄 방법*을 넣는다.
  - `InvocationHandler`는 프록시에 호출되는 모든 메소드에 응답한다. 
  - Proxy에서 메소드 호출을 받는다 → `InvocationHandler`에 진짜 작업을 부탁한다.
![보호프록시](https://t1.daumcdn.net/cfile/tistory/266EE435543B43B112)

### 동적 프록시 만드는 방법

#### 1. InvocationHandler 만들기
InvocationHandler는 프록시의 행동을 구현한다.

#### 2. 동적 프록시 생성 코드 만들기
프록시 클래스를 생성하고 그 인스턴스를 만드는 코드를 쓴다.

#### 3. 적절한 프록시로 객체 감싸기

###### reference

> HeadFirst Design Pattern   