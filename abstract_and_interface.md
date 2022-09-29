# 추상클래스와 메서드와 인터페이스

## 추상 클래스 개요

- 추상메서드 : 처리 내용 기술하지 않는 메서드
- 추상클래스 : 추상 메서드를 갖는 클래스
  - 인스턴스할 수 없음

> 지금까지 본 것은 구상클래스,
> 이 장에서 볼 것은 추상 클래스

**인스턴스할 수 없기에, 계승하면 추상 메서드를 오버라이드한 서브클래스 정의 필요**

그럼 이런 수고가 더 드는 추상 클래스가 왜 필요한가?

힌트는 오버라이드에 있다.

```
오버라이드는, 슈퍼클래스에 정의된 메서드를 서브클래스 내에 재정의하는 것.
즉, 슈퍼클래스 메서드를 서브클래스 용으로 쓸 수 있게함
오버라이드 정의하기 위해서는 규약이 있다.

- 오버라이드에는 규칙이 있음
  - 반환값 타입, 메서드명, 인수타입, 인수의 개수, 인수의 정렬순서가 같아야한다.
  - 슈퍼 클래스를 넘는 넓은 범위에 접근 수식자 필요하다(거의 public)

```

**오버라이드 규칙을 안지키면 컴파일 에러가 나온다**

그래서 반환값의 타입, 메서드명, 인수타입, 인수의 수, 인수 정렬 순서 메서드 밖에 정의불가

그렇기에 복수 클래스를 통일화해서 안전하게 정의가능.

추상메서드 선언된 최저한의 메서드를 보증함

## 표기

```java
수식자 abstract class 클래스명 {};

수식자 abstract 반환값타입 메서드명(인수 리스트);
```

- 어차피 처리내용이 없으니 {}를 안써도 됨

- static, fnial, private 부여 불가.
  - 수식자가 허용되면 처리가 없는 추상 메서드를 호출할 수 있음
  - 추상 메서드를 오버라이드할 수 없음

```java
abstract class AbstractSuper {
  abstract void abMethod();   // 추상 메서드
  public void method(){};     // 통상 메서드
}
```

```java
public class AbstractSub extends AbstractSuper {
  void abMethod(){};    // 추상메서드 오버라이드 필수
}
```

## 인터페이스 개요

인터페이스란 **접점**이라는 의미

**객체를 이용할 때 공통사양**

### 예시

에어컨 온도 조절할 때 리모콘을 사용한다.

가령 에어컨을 바꾸면, 세세한 조작은 바뀔지 몰라도, 기본 동작은 같음

▲를 누르면 온도가 올라가고, ▼를 누르면 온도가 내려간다는 것을 알고 있다.

어떤 것에서도 공통적 버튼으로 동작을 하기 때문이다.

즉, **객체를 이용할 때 공통사양을 모아둔 것.** 이를 인터페이스라 함

### 다형성

인터페이스에 의해 공통 사양을 구현.

일정한 기능 상에 여러 기능을 구현할 수 있음.

에어컨 버튼에서 온도조절 버튼은 같게 만들되, 그 외에 환풍이나 인공지능 기능 등 새로운 동작을 부여할 수 있음

이를 **다형성**이라함

### 선언

```java
수식자 (abstract) interface 인터페이스명{}
```

- 추상클래스처럼 인스턴스 불가.
- 변수는 상수만 가능.
- 메서드는 추상 메서드만 가능.
- 인터페이스 선언하는 변수와 추상 메서드는 클래스 멤버와 다르기에, **초기값 필수**
- 변수와 추상메서드에는 암묵적 수식자 부여됨
  - 변수: public static final
  - 메서드: public abstract
  - 이로 인해 각각의 성질을 지니게 됨

### 구현

```java
수식자 class 클래스명 implements 인터페이스명 {}
```

- implements: 계승과 유사한 키워드
- `,`를 통해 복수 지정 가능
- 추상클래스와 같이 인터페이스는 구현한 클래스에 추상 메서드 모두 오버라이드 필수

### 예시

```java
interface Interface {
  public static final int x = 10;
  public abstract void method();
}
```

```java
public class InterfaceClass implements Interface {
  public void method(){};
}
```

- 추상 클래스와 병용 사용도 가능
- 대신 추상 클래스 실현(extends)를 인터페이스 실현(implements)보다 앞에 써야함

```java
public class InterfaceClass extends AbstractSuper implements Interface {
  void abMethod(){};
  public void method(){};
}
```

### 인터페이스 계승

- 인터페이스는 서브 인터페이스로 계승 가능

```java
수식자 interface 서브인터페이스 extends 슈퍼인터페이스 {}
```

- **서브인터페이스 메서드는 반드시 구현이 필요함**

```java
intetface A {
  void method1();
}

intetface B {
  void method2();
}

intetface C extends A, B {   //A, B 인터페이스 계승
  void method3();
}

class InterClass implements C {
  public void method1(){}  // 슈퍼인터페이스 A 메서드 구현
  public void method2(){}  // 슈퍼인터페이스 B 메서드 구현
  public void method3(){}  // 서브인터페이스 C 메서드 구현
}
```

### 익명클래스

인터페이스 내에 클래스를 구현하는 익명 클래스

```java
// 익명 클래스 인스턴스 생성
Runnable runnable = new Runnable() {
    public void run() {
        // 비동기처리
        ...
    }
};

// runnable를 다른 스레드에서 비동기실행
new Thread(runnable).start();
...
```

```java
// Runnable 익명 클래스 인스턴스 생성해서, 다른 스레드에서 비동기 실행
new Thread(new Runnable() {
    public void run() {
        // 비동기 실행
        ...
    }
} ).start();
```

```java
// InterfaceName : 인터페이스 명、var : 변수명
[ InterfaceName var = ] new InterfaceName () {
    // 메서드 정의(모든 추상 메서드 구현)
    [ method [ method ... ] ]
}
```

## 추상클래스와 인터페이스

|          | 추상클래스        | 인터페이스              |
| -------- | ----------------- | ----------------------- |
| 인스턴스 | 생성불가          | 생성불가                |
| 다중계승 | 불가(하나만 가능) | 가능(복수가능)          |
| 멤버     | 뭐든지 정의가능   | 상수, 추상메서드만 가능 |
