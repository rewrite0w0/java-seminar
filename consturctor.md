# constructor

클래스의 인스턴스 객체를 생성, 초기화하는 메서드

```java
수식자 constructor명(클래스와 동일) (인수타입 인수명){};
```

- 가능:public,protected,private 등
- 불가: static,abstract,final 등

## 호출

```java
// 초기값 미지정
new 컨스트럭터();

// 초기값 지정
new 컨스트럭터(인수);
```

### 예시

```java
public class SetNumber {
  private int num1;
  private int num2;

  // int 타입 인수를 갖는 컨스트럭터1
  SetNumber (int i) {
    this.num1 = 100;
    this.num2 = i;
  }
  // 인수가 없는 컨스트럭터2
  SetNumber () {
    this.num1 = 100;
    this.num2 = 500;
  }

  public void getNumber(){
    System.out.println(num1);
    System.out.println(num2);
  }
}
```

```java
public class SetNumberMain {
  public static void main(String[] args) {
    // 인수가 있는 1 호출
    SetNumber numA = new SetNumber(1000);
    // 인수가 없는 2 호출
    SetNumber numB = new SetNumber();
    numA.getNumber();
    numB.getNumber();
  }
}
```

```bash
100
1000
100
500
```

## 다른 컨스트럭터 호출

- 위의 1, 2에는 `this.num1 = 100`이 있다.
- this()를 사용해서 의해 다른 컨스트럭터 호출하는 방법도 있다.

```java
public class SetNumber {
  private int num1;
  private int num2;

  // 인수가 있는 1
  SetNumber (int i) {
    this.num1 = 100;
    this.num2 = i;
  }
  // 인수가 없는 2
  SetNumber () {
    this(500);  // 변경하는 곳
  }

  public void getNumber(){
    System.out.println(num1);
    System.out.println(num2);
  }
}
```

## default constructor

- 위의 예시에서는 `SetNumber`가 명시적 정의
- 다만 다른 예시에서는 그렇지 않았다.
- 그렇지만 `new 컨스트럭터이름`을 통해 인스턴스 가능했음.
- 이는 컨스트럭터가 명시되어 있지 않을 때, 자동적으로 컨트스턱터를 추가해주기 때문
- 이를 `default constructor`라 함

## 슈퍼 클래스 컨스트럭터 호출

- 클래스를 계승할 때, 컨스트럭터는 서브클래스에 계승되지 않음.
- 서브 클래스에서 슈퍼클래스 컨스트럭터를 호출할 때 **super**사용

### 예시

```java
public class SuperClass {
  private int val;

  // 컨스트럭터
  SuperClass(int a) {
      val = a;
  }
  public void getVal(){
      System.out.println(val);
  }
}
```

```java
public class SubClass extends SuperClass {
  // 컨스트럭터
  SubClass(){
    // super로 슈퍼클래스 컨스트럭터 호출
    super(10);
  }
}
```

```java
public class SuperSubMain {
  public static void main(String[] args){
    SubClass sub = new SubClass();
    sub.getVal();
  }
}
```

`super(10)`를 통해 슈퍼클래스 컨스트럭터(SuperClass(int a) 호출)

호출된 SuperClass(int a) 인수에 10를 넘길 뿐
