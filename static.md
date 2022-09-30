# static

## 결론

- js에서 `const`
- 불변한 것
- 멤버(멤버 변수, 메서드)에 부여

## 멤버변수 종류

| 표기   | 멤버변수     | 멤버메서드     | 다른 클래스에서 호출 |
| ------ | ------------ | -------------- | -------------------- |
|        | 인스턴스변수 | 인스턴스메서드 | 인스턴스화 필수      |
| static | static변수   | static메서드   | 인스턴스화 필요없음  |

### 예시

```java
// InstanceStatic.java

public class InstanceStatic {
    // 인스턴스 변수 선언, 초기화
    int instanceVal = 10;

    // static 변수 선언, 초기화
    static int staticVal = 100;

    // 인스턴스 변수 선언
    void instanceMethod(){
      System.out.println(instanceVal);
    }

    // static메서드 선언
    static void staticMethod(){
      System.out.println(staticVal);
    }
}
```

```java
// CallInstanceStatic.java

public class CallInstanceStatic {
  public static void main(String[] args){
    //InstanceStatic 클래스의 static 변수 호출
    System.out.println(InstanceStatic.staticVal);

    //InstanceStatic 클래스의 static 메서드 호출
    InstanceStatic.staticMethod();

    //InstanceStatic 클래스의 인스턴스화 해서 x에 대입
    InstanceStatic x = new InstanceStatic();

    //x 인스턴스 변수 호출
    System.out.println(x.instanceVal);

    //x인스턴스 메서드 호출
    x.instanceMethod();
  }
}
```

```bash
100
100
10
10
```

- 인스턴스 변수, 메서드는 인스턴스화 후에 사용할 수 있음
- 그래서 인스턴스화 하지 않으면 참조할 수 없어 컴파일 에러
- 다만 같은 클래스 안에서는 인스턴스화 하지 않아도 변수, 메서드를 사용할 수 있음.
- static 메서드에서 인스턴스 변수 참조하는 경우에만 인스턴스하면 됨.

```java
public class InstanceStatic {
    // 인스턴스 변수
    int instanceVal = 10;
    //static 변수
    static int staticVal = 100;
    //인스턴스 메서드
    void instanceMethod(){
      System.out.println(instanceVal);
    }
    //static 메서드
    static void staticMethod(){
      System.out.println(staticVal);
    }

        // 인스턴스 메서드 => 인스턴스 변수(문제x)
    int A(){
      return instanceVal;
    }

    //인스턴스 메서드 => static변수(문제x)
    int B(){
      return staticVal;
    }

    //static 메서드 => static 변수 (문제x)
    static int C(){
      return staticVal;
    }

    //static 메서드 => 인스턴스 변수(컴파일 에러)
    static int D(){
      return instanceVal;
    }

    //static 메서드 => 인스턴스 변수(인스턴스화 하면 문제x)
    static int D(){
      InstanceStatic y = new InstanceStatic();
      return y.instanceVal;
    }
}
```

## static / 비static 멤버 참조 표기

| 멤버     | 멤버변수                            | 메서드                                    |
| -------- | ----------------------------------- | ----------------------------------------- |
| static   | 클래스명.멤버변수                   | 클래스명.메서드명(인수)                   |
| 인스턴스 | 인스턴스주소값를담은변수명.멤버변수 | 인스턴스주소값을담은변수명.메서드명(인수) |

### 예시

```java
// FootballPlayer.java
public class FootballPlayer {
  public static String gender;
  public String name;
  public int age;
  public void Self(String msg){
    System.out.println("이름:" + name);
    System.out.println("나이:" + age);
    System.out.println("성별：" + gender);
    System.out.println("자기소개：" + msg);
    System.out.println("-------------------------");
  }
}

```

````java
// FootballPlayertMain.java

public class FootballPlayertMain {
  public static void main(String[] args){

    FootballPlayer.gender = "남자";

    FootballPlayer ronaldo = new FootballPlayer();
    FootballPlayer haaland = new FootballPlayer();

    ronaldo.name = "호날두";
    ronaldo.age = 39;

    haaland.name = "홀란드";
    haaland.age = 22;

    ronaldo.Self("호날두이다.");
    haaland.Self("홀란드이다.");
  }
}

```bash
이름: 호날두
나이: 39
자기소개: 호날두이다.
성별: 남
----------------------------
이름: 홀란드
나이: 22
자기소개: 홀란드이다.
성별: 남
----------------------------
````

- 여기서 `static`를 작성하지 않아도 클래스를 사용할 수 있음
- 그 이유는 2가지 조건 중 하나를 충족했기 때문
  1. 자기 인스턴스 인스턴스멤버 참조할 때
  2. 자기 클래스 static 멤버를 참조할 때
- 상기 예시는 Self 메서드 실행시, `호날두`, `홀란드` 자신 인스턴스를 특정.
- 이에 name, age 자기 인스턴스를 인스턴스 멤버로 참조할 수 있는 걸
- 즉, static이면서 같은 값을 갖는 것이 static 멤버(위의 예시는 gender)
- 반대로 각각 인스턴스가 다른 값을 갖는 인스턴스 멤버(비 static멤버, name, age)
