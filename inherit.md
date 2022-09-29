# 클래스, 계승

- 클래스의 장점은 하나의 클래스에서 여러가지 만드는 것.
- 기본 카레 레시피(클래스)에 치즈 넣으면 치즈 카레(클래스), 닭은 넣으면 치킨 카레(클래스).
- 이는 치즈 카레와, 치킨 카레가 기본 카레에게 **계승**을 받은 것.
- 계승의 근간이 되는 클래스는 **슈퍼클래스**
- 기본 카레는 **슈퍼클래스(부모클래스)**

```java

class 서브클래스 extends 슈퍼클래스{}

class Curry {
    void exShow(){
        System.out.println("카레는 3분이면 끝")
    }
}


class cheeseCurry extends Curry{
    void exCheeseShow(){
        system.out.println("치즈 카레는 3분 카레에 치즈만 뿌리면 끝")
    }
}
```

```java

class Main{
    public static void main(String[] args){
        CheeseCurry ins1 = new CheeseCurry(); // 치즈카레 클래스 인스턴스 생성
        ins1.exShow() // 슈퍼클래스 메서드 호출
        ins1.exCheeseShow() // 서브클래스 메서드 호출
    }
}

```

```bash
javac Main.java
java Main
카레는 3분이면 끝
치즈 카레는 3분 카레에 치즈만 뿌리면 끝
```

## 계승 규칙

- 계승은 다중으로 받을 수 없습니다.(다중계승 불가) (즉, 여러 클래스 => 1개 클래스 불가)
- 하나의 클래스에서 복수 클래스 계승 받을 수 있음 (1개 클래스 => 여러 클래스)
- 계승 받은 클래스를 또 계승 시킬 수도 있음(클래스 => 클래스 => 클래스)

## 오버라이드

- ~~오버로드와는 다르다 오버로드와는!~~
- **슈퍼클래스 정의된 메소드를 서브클래스 안에서 재정의하는 것**
- 즉, 슈퍼 클래스 머스드를 서브클래스 용으로 겹쳐 쓰는 것(덮어쓰기가 아님)
- 오버라이드에는 규칙이 있음
  - 반환값 타입, 메서드명, 인수타입, 인수의 개수, 인수의 정렬순서가 같아야한다.
  - 슈퍼 클래스를 넘는 넓은 범위에 접근 수식자 필요하다(거의 public)

### 예시

```java
// ExClass.java
class ExClass {
    public void increase(int i) {
        int x = i * 10;
        System.out.println(x);
    }
}
```

```java
// ExSubClassMain.java
class SubExClass extends ExClass {
    public void increase(int i) {
        int x = i * 1000;
        System.out.println(x);
    }
}
```

```java
// ExSubClassMain.java
public class ExSubClassMain {
  public static void main(String[] args){
    // 서브클래스 인스턴스화
    SubExClass sub = new SubExClass();
    sub.increase(5);
  }
}
```

```bash
5000
```

## 슈퍼클래스 메서드 명시적 호출

- 오버라이드 하면, 슈퍼클래스 위에는 서로는 것이 있는 상태
- 이럴 때 슈퍼클래스를 사용하고 싶다면, `super.메서드명`사용

### 예시

```java
// ExClass.java
class ExClass {
    public void increase(int i) {
    int x = i * 10;
    System.out.println(x);
    }
}

```

```java
// ExSubClassMain.java
class SubExClass extends ExClass {
    public void increase(int i) {
        int x = i * 1000;
        System.out.println(x);
    }

    public voide supaIncrease(int i){
        super.increase(i)
    }
}
```

```java
// ExSubClassMain.java
public class ExSubClassMain {
  public static void main(String[] args){
    // 서브클래스 인스턴스화
    SubExClass sub = new SubExClass();
    sub.increase(5);
    sub.supaIncrease(5); // 슈퍼클래스 메서드 호출
  }
}
```

```bash
5000
50
```

## final

- 멤버, 클래스에 부여가능, 기본 수정 불가
- 멤버변수: **상수**
- 메소드: **오버라이드 불가**
- 클래스: **계승 불가**

### 예시

```java
final int num = 100;
num = 200;  // 컴파일 에러

class Super {
   final void method(){};
}

class Sub extends Super {
   void method(){};  // 컴파일 에러
}

class Sub extends Super {
   void method(){};  // 컴파일 에러
}
```

## private 컨스트럭터 사용방법

- private 컨스트럭터는 계승에 제한이 있기에 이를 활용하기 위한 방법이 따로 존재
- 유틸리티 클래스, 팩토리 메서드, 싱글톤이 대표적

### 유틸리티 클래스

- 유틸리티 클래스: `static` 메서드와 상수 필드만 가지고 있는 클래스
- Java 표준 API에서는 `Math` `Collections` 클래스가 대표적
- 인스턴스 생성할 필요없음
- 계승 또한 필요없음
- `final` 표기해 계승하지 않음을 명시해야함
- 인수가 없는 `private` 컨스트럭터만 정의해 계승과 인스턴스 생성 모두 제어

```java
public final class Math {
    private Math() {

    }

    public static int max(int a, int b) {
        return (a >= b) ? a : b;
    }
    ...
}
```

### 팩토리 메서드

- 인스턴스 생성시, 컨스트럭터 초기화해서 사용해야하는 경우는 팩토리 메서드
- `public` `static` 메서드 정의
- 컨스트럭터를 `private` 계승 허용하고 싶드면 `protected` 스코프 정의
- 팩터리 메서드는 처리 은폐

```java
public final class LocalDate {

    private LocalDate(int year, int month, int dayOfMonth) {
        ...
    }

    public static LocalDate of(int year, int month, int dayOfMonth) {
        // 실제 LocalDate 에서 복수 메서드를 처리할 수 있지만 여기서는 생략

        ...
        return new LocalDate(year, month, dayOfMonth);
    }
    ...
}
```

- `LocalDate` 클래스는 날짜를 표현
- 날짜를 여러 표현이 있음(년-월-일, 년-월이름-일 등등)
- 불변 클래스이므로 인스턴스 필요
- 이 모든 것을 담은 클래스를 구현하기 어렵기에 컨스트럭터는 `private`로 은폐 후 팩터리 메서드를 사용

### 싱글톤

- 인스턴스를 오직 하나만 생성하는 클래스를 실현하는 테크닉
- 애플리케이션 전체 공유하고 싶은 정보를 담기 위해 사용

```java
public final class Singleton {
    // private 한 static 필드에 유일한 인스턴스 설정
    private static final Singleton instance = new Singleton();

    private Singleton() {
        ...
    }

    public static Singleton getInstance() {
        // instance 값은 늘 같으므로 이 메서드는 반환값은 늘 같음
        return instance;
    }
    ...
}
```

- private 스쿠프 정의되어 있으므로, 다른 클래스나 일반적 메서드는 인스턴스 생성 불가
- final + static 필드 인스턴스 초기값은 인스턴스 생성(컨스트럭터 호출)함으로 instacne 값은 변경할 수 없으며, 인스턴스는 static 필드 instance에 설정된 유일한 값이 됨
- static 메서드 getInstace는 초기화 된 instace 값을 반환하므로 늘 같은 값
