# 클래스

## TL;DR

클래스는 객체를 만들기 위한 **설계도**

인스턴스는 클래스를 **인스턴스화**

오버로드는 **하나의 이름으로 여러 메서드를 정의할 수 있는 방법**

## 클래스

- 클래스는 **객체를 만들기 위한 설계도**
- **필드(상태)**와 **메서드(기능)**을 묶어둔 것
- 필드: 클래스 내 변수 정의 **(멤버 변수)**
- 메서드: 해당 메서드 내 변수 정의 **(로컬 변수)**

### 표 정리

| 클래스 안에서의 위치 | 특성 | 변수를 부르는 방법 | 변수 스코프   |
| -------------------- | ---- | ------------------ | ------------- |
| 필드                 | 상태 | 멤버변수           | 클래스 안에서 |
| 메서드               | 기능 | 로컬변수           | 메서드 안에서 |

### 멤버 변수 선언 방법

```java
// 1
(수식자) 자료형 멤버 변수명;

// 2
(수식자) 자료형 멤버변수명 = 값;

```

### 메서드 정의

```java
(수식자) 반환값자료형 메서드명(인수리스트) {}
```

#### 예시

1. 코드 작성

```java
// Curry.java

public class Curry {
    String name = "오뚜기 카레"
    public String taste(){
        return "맵지만 맛난 카레";
    }
}

```

2. 컴파일

```bash
javac Curry.java
```

#### 반환값이 필요없을 때는 **void**

```java
(수식자) void 메서드명(인수리스트) {}
```

#### 반환값이 필요할 떄는 **return**

```java
return 반환값;
```

## 인스턴스

클래스는 설계도, 이를 실체화 하기 위해서는 **인스턴스화**해서 **인스턴스** 생성

```java
클래스명 변수명 = new 클래스(인수명);

```

```java
public class Sample {
  public static void main(String[] args) {
    // Curry 클래스 인스턴스화
    Curry oddugi = new Curry();
  }
}
```

사용방법은 `JS` 객체와 닮음

```java
public class Sample {
  public static void main(String[] args) {
    // Curry 클래스 인스턴스화
    Curry curry = new Curry();

    // Curry 클래스 필드 값
    System.out.println(curry.name);

    // Curry 클래스 메서드 실행
    System.out.println(curry.taste());
  }
}
```

```bash
오뚜기 카레
맵지만 맛난 카레
```

##### 여담, JS 닮았다는 이유

```js
const obj = {
  name: "오뚜기 카레",
  taste: () => {
    return console.log("맵지만 맛난 카레");
  },
};
console.log(obj.name); // 오뚜기 카레
obj.taste(); // 맵지만 맛난 카레
```

## 오버로드

**하나의 클래스 안에서 메서드 명이 같은 여러 메서드를 정의할 수 있다.**

```java
class Overload{
    void status(String name, String color) {  // 인수가 2개인 status 메서드
        System.out.println("오버로드의 이름" + name);
        System.out.println("색은" + color);
    }
    void status(String name) {  // 인수가 1개인 status 메서드
        System.out.println("오버로드의 이름" + name);
        System.out.println("색은" + "빨강");
    }
    public static void main(String[] args) {
        Overload over1 = new Overload();
        ins1.status("꾸에에엑");
        // 오버로드의 이름 구에에엑
        // 색은 빨강

        Overload over2 = new Overload();
        ins2.status("꾸에에엑", "파랑")
        // 오버로드의 이름 구에에엑
        // 색은 파랑
    }
}
```

### 오버로드 할 수 없는 경우

1. 인수 명수명이 다른 경우
2. 반환 값이 다를 경우

#### 1. 인수 변수명이 다른 경우

```java
void status(String country) {  // status 메서드 인수명 country
        ...
    }
void status(String name) {  //status 메서드 인수명 name
        ...
    }
```

#### 2. 반환 값이 다를 경우

```java
void status(String name) {  //status 메서드 반환 값 타입 void
        ...
    }
String status(String name) {  //status 메서드 반환 값 타입 String
        ...
    }
```

위의 2가지일 때는 오버로드 불가능
