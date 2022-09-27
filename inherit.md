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
