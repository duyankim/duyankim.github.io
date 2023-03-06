---
layout: post
author: "Yan"
catalog: true
subtitle: "토비의 스프링을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 템플릿 콜백"
date: 2023-03-06 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 템플릿 콜백
comments: true
---

# 탬플릿/콜백

- 복잡하지만 바뀌지 않는 일정한 패턴을 갖는 작업흐름이 존재하고 그 중 일부분만 자주 바꿔서 사용해야 하는 경우에 적합한 구조.
- 전략 패턴의 기본 구조에 익명 내부 클래스를 활용한 방식
- 전략 패턴의 컨텍스트를 `템플릿`이라 부르고, 익명 내부 클래스로 만들어지는 오브젝트를 `콜백`이라고 부른다.

### 템플릿

- 고정된 틀 안에 바꿀 수 있는 부분을 넣어서 사용하는 경우
- 템플릿 메소드 패턴은 고정된 틀의 로직을 가진 템플릿 메소드를 `슈퍼클래스`에 두고, 바꾸니느 부분을 `서브클래스`의 메소드에 두는 구조로 이루어진다.

### 콜백

- 실행되는 것을 목저으로 다른 오브젝트의 메소드에 전달되는 오브젝트를 말한다.
- 파라미터로 전달되지만 값을 참조하기 위한 것이 아니라 특정 로직을 담은 메소드를 실행시키기 위해 사용한다.
- 자바에선 메소드 자체를 파라미터로 전달할 방법이 없기 때문에 메소드가 담긴 오브젝트를 전달해야 한다 (functional object)

## 템플릿/콜백의 특징

- 여러 개의 메소드를 가진 일반적인 인터페이스를 사용할 수 있는 전략 패턴의 전략과 달리 콜백은 보통 단일 메소드 인터페이스를 사용한다.
- 콜백은 일반적으로 하나의 메소드를 가진 인터페이스를 구현한 익명 내부 클래스로 만들어진다.
- DI방식의 전략패턴 구조

#### 예제

파일의 숫자 합을 계산하는 코드의 테스트

```java
public class CalcSumTest {
  @Test
  public void sumOfNumbers() throws IOException {
    Calculator calculator = new Calculator();
    int sum = calculator.calcSum(getClass().getResource(
      "numbers.text").getPath());
    assertThat(sum, is(10));
  }
}
```

Calculator 클래스 코드 1

```java
public class Calculator {
  public Integer calcSum(String filepath) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(filepath));
    Integer sum = 0;
    String line = null;
    while((line = br.readLine()) != null) {
      sum += Integer.valueOf(line);
    }

    br.close();
    return sum;
  }
}
```

try/catch/finally를 적용한 calcSum()메소드

```java
public Intger calcSum(String filepath) throws IOException {
  BufferedReader br = null;

  try {
    br = new BufferedReader(new FileReader(filepath));
    Integer sum = 0;
    String line = null;
    while((line = br.readLine()) != null) {
      sum += Integer.valueOf(line);
    }
    return sum;
  }
  catch(IOException e) {
    System.out.println(e.getMessage());
    throw e;
  }
  finally {
    if (br != null) {
      try {
        br.close();
      } catch(IOException e) {
        System.out.printlne(e.getMessage());
      }
    }
  }
}
```

템플릭 콜백 패턴 적용  

1. 템플릿에 담을 작업 흐름은 어떤 것인가?
2. 템플릿이 콜백에게 전달해줄 내부의 정보는 무엇인가?
3. 콜백이 템플릿에게 돌려줄 내용은 무엇인가?

BufferedReader를 전달받는 콜백 인터페이스

```java
public interface BufferedReaderCallback {
  Integer doSomethingWithReader(BufferedReader br) throws IOException;
}
```

BufferedReaderCalback을 사용하는 템플릿 메소드

```java
public Integer fileReadTemplate(String filepath, BufferedReaderCallback callback) throws IOException {
  BufferedReader br = null;

  try {
    br = new BufferedReader(new FileReader(filepath));
    int ret = callback.doSomethingWithReader(br);
    return ret;
  }
  catch(IOException e) {
    System.out.println(e.getMessage());
    throw e;
  }
  finally {
    if (br != null) {
      try {
        br.close();
      } catch(IOException e) {
        System.out.printlne(e.getMessage());
      }
    }
  }
}
```

템플릿/콜백을 적용한 calcSum() 메소드
```java
public Integer calcSum(String filePath) throws IOException {
  BufferedReaderCallback sumCallback = new BufferedReaderCallback() {
    public Integer doSomethingWithReader(BufferedReader br) throws IOException {
      Integer sum = 0;
      String line = null;
      while((line = br.readLine()) != null) {
        sum += Integer.valueOf(line);
      }
      return sum;
    }
  };
  return fileReadTemplate(filepath, sumCallback);
}
```

새로운 테스트 메소드를 추가한 CalcSumTest

```java
public class CalcSumTest {
  Calculator calculator;
  String numFilepath;

  @Before
  public void setUp() {
    this.calculator = new Calculator();
    this.numFilePath = getClass().getResource("numbers.txt").getPath();
  }

  @Test
  public void sumOfNumbers() throws IOException{
    assertThat(calculator.calcSum(this.numFilepath), is(10));
  }
}
```

###### reference

> 토비의 스프링 3.1 vol.1