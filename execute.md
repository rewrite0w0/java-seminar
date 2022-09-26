# Java 작성 및 실행

## Java 작성

### 클래스 정의

```java
접근수식자 class 클래스명 {}

public class Calc {}
```

### 멤버 변수 정의

```java
접근수식자 static수식자 final수식자 자료형 변수명;
// stats/fianl 생략가능

private String name;
```

### 메서드 정의

```java
접근수식자 static수식자 final수식자 반환값자료형 메서드명(인수자료형 인수명){
    처리
    return 반환값;
}
// stats/fianl/return 생략가능

public int number(int[] people){
    int num = 10;
    return num;
}

```

### 메인 메서드 정의

```java
public static void main (String[] args){}
```

위의 예시에서 거의 벗어나지 않게 선언됨

또 위의 예시를 풀어보면

- public: 어디서든 접근 가능
- static: 다른 수식자
- void: 반환값 타입(void는 반환값이 없는 경우)
- main: 메서드명(반드시 필요함)
- String[] args: 인수(main 메서드라면 이렇게 쓰는 것이 기본, args와 다른 이름이어도 됨)

### 수식자

접근 여부를 부여하는 것

| 접근수식자 | 클래스지정 | 접근가능범위                             |
| ---------- | ---------- | ---------------------------------------- |
| public     | O          | 어디서든                                 |
| protected  | X          | 같은 클래스, 패키지, 서브클래스에서 가능 |
| 지정없음   | O          | 같은 클래스, 패키지에서 가능             |
| private    | X          | 같은 클래스 안에서만 가능                |

일반적으로 멤버변수(필드, 클래스 상태)는 `private`, 클래스, 메서드는 `public` 지정

`protected`, `private`로는 클래스 지정할 수 없음

#### 예시

```java
public class Access {
  //private 인스텬스 변수
  private int A;

  //public 컨스트럭터
  public Access(int B){
    A = B;
  }

  //public 메서드
  public int getB(){
    return A;
  }
}
```

```java
public class AccessModifier {
  public static void main(String[] args){
    // 인스턴스화
    Access acc = new Access(10);
    // System.out.println(acc.A);   // 컴파일 에러
    System.out.println(acc.getB());
  }
}
```

- private 인스턴스 변수는 참조할 수 없음.
- public은 컨스트럭터, 메서드 참조 가능

### 그 외 수식자

- static: 클래스 변수, 클래스 메서드 선언
- abstract: `인스턴스 생성불가`
- final: 상수 선언

### 변수, 메서드 선언

1. 클래스 변수, 클래스 메서드 (static)
2. 인스턴스 변수, 인스턴스 메서드 (비 static)

#### 1. 클래스 변수, 클래스 메서드 (static)

- 인스턴스 생성 시에, 변수, 메서드는 할당되지 않음.
- static 수식자가 있는 것이 클래스의 변수, 메서드가 된다.

#### 2. 인스턴스 변수, 인스턴스 메서드 (비 static)

- 인스턴스 생성 시에 변수, 메서드가 할당된다.
- static이 붙어있지 않은 것이 인스턴스의 변수, 메서드가 된다.

### 반환값 자료형

간략 예시

- void: 반환값 X
- int: 정수
- String: 문자열

## Java 실행

**컴파일 후 실행**

```bash
javac 파일명
java 클래스명
```

### 예시

```java
class Main {
  public static void main(String[] args) {
    String greeting = "Hello World!";
    System.out.println(greeting);
  }
}
```

```bash
javac Main.java
java Main
# .class 필요없음
# Hello World!
```

### 어노테이션

- **주석과는 닮았지만 다르다**

- 어노테이션은 컴파일러가 이를 인식

```java
@문자열
// 주로 쓰이는건 이렇게 3개
@Override // 오버라이드 선언
@Deprecated // 해당 클래스, 멤버 비권장하는 선언
@SuppressWarnings // 컴파일러 경고를 보이지 않도록 하는 선언

// 여담으로 자작도 가능

@author: rewrite
```
