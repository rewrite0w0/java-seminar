# 참조형

## String타입

- String은 문자열 리터럴이라고 여겨질 수 있음
- 하지만 엄밀히 말하면 **참조형 클래스**
- JavaSE API에 포함된 것, JDK 인스톨하면 같이 설치됨
- String 클래스 멤버변수: char 배열타입 변수
- String 클래스 메서드: char 배열 조작 기능

```java
String str ="abc";
// 이것은 추상적으로보면
char arr[] = {'a','b','c'}
```

## String 인스턴스 생성

```java
// 1.
String 변수명 = "문자열";

// 2.
String 변수명 = new String("문자열");
```

`1`이 대부분의 방법(메모리 소비 적음)

constant pool(Java가상머신 메모리 내 힙영역 일부)참조해서 메모리 주소를 반환

**이렇기에 인스턴스 생성하지 않고 참조할 수 있음**

```
val1 =>
ㅤㅤㅤㅤㅤㅤㅤconstant pool <주소(100) String 인스턴스 char[]?={'a','b','c'}>
val2 =>
```

```java
String val1 = new String("abc");
String val2 = new String("abc");
```

```
val1 => 힙<주소(100) String 인스턴스 char[]?={'a','b','c'}>

val2 => 힙<주소(200) String 인스턴스 char[]?={'a','b','c'}>
```

이런식으로 메모리 하나 더 씀

## 결론

| 서식                 | 인스턴스       | 생성처           |
| -------------------- | -------------- | ---------------- |
| "문자열"             | 생성 혹은 참조 | constant pool 안 |
| new String("문자열") | 생성           | 힙 안            |
