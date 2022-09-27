# 자료형 변환

## 개요

변수는 2종류 변환이 가능

1. 암묵적 타입변환
2. 명시적 캐스트 타입변환

java의 타입은 8가지

1. byte
2. short
3. int
4. long
5. float
6. double
7. char
8. boolean

### 1. 암묵적 타입변환

- 대입시
- 메서드 인수 호출 시
- 메서드 반환 값 호출 시

```Java
// 대입시
short x = 10;
int y = x;
System.out.println(y);
```

```java
// 메서드 인수 호출 시
public static void method(double x){
  System.out.println(x);
}

int i = 10;
method(i);
```

```java
// 메서드 반환 값 호출 시
int method(){
  int i = 10;
  return i;
}

double x = method();
System.out.println(x);
```

### 2. 명시적 캐스트 타입변환

- 선언
- ()캐스트 연산자 사용

```java
(타입) 값
double a = 10.5;
int b = (int) a; // 10
```

## 참조자료형 타입변환

위와 같이 2종류

1. 암묵적 타입변환
2. 명시적 캐스트 타입변환

- 암묵: 서브클래스 => 슈퍼클래스
- 명시: 구현클래스 => 인터페이스

### 1. 암묵적 타입변환

```java
abstract class Ab {
  int x = 10;
  int y = 20;
  public abstract void method1();
  public void method2(){
    System.out.println(y);
  };
}
```

```java
public class Sub extends Ab {
  public void method1(){
    System.out.println(x);
  }
}
```

```java
public class SubMain {
  public static void main(String[] args){
    Ab a = new Sub();
    a.method1();
    a.method2();
  }
}
```

```bash
10
20
```

### 2. 명시적 캐스트 타입변환

```java
(변환후타입) 인스턴스주소값을갖는변수명
```

```java
public class SubMain {
  public static void main(String[] args){
    Ab a = new Sub();
    Sub sub = (Sub)a;
    sub.method1();
    sub.method2();
  }
}
```

```bash
10
20
```
