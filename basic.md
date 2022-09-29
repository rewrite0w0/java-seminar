# Java 기본

## 변수선언

```java
자료형 변수명;
int number;

변수명 = 값;
number = 10;

자료형 변수명 = 값;
int number = 10;
```

## 자료형

크게 나누면 이하와 같음

- 정수형
  - byte: -128 ~ 127
  - short: -3만 ~ 3만
  - int: -20억 ~ 20억
  - long: int 이상, 이하
    - 말미에 `L` 표기해야함
- 소수형
  - float: 메모리 사이즈 32bit
  - double: 메모리 사이즈 64bit(더 세세함)
- 문자형
  - char: 1글자(')
  - String: 2글자 이상(")
- 불린형
  - boolean: true or false
    - 초기값은 false

## 명명규칙

| 클래스    | 메서드          | 변수                   |
| --------- | --------------- | ---------------------- |
| 명사      | 동사            | 의미있게               |
| CamelCase | 첫글자만 소문자 | 문자를 사용하지 않는다 |

## 기본형과 참조형

1. 기본형
2. 참조형

| 자료형 | 변수에 넣을 수 있는 것 | 해당하는 자료형                               |
| ------ | ---------------------- | --------------------------------------------- |
| 기본형 | 값                     | boolean,byte,char,short,int,float,long,double |
| 참조형 | 객체 또는 배열 참조값  | String, 배열                                  |

## 리터럴

리터럴은 값을 **표현하는 방법**

예를 들어 `10`이라면 10진수에서 10으로도 보지만, 2진수의 1, 0 일 수도 있다.

### 정수 리터럴

| 진수   | prefix | 예시   |
| ------ | ------ | ------ |
| 10진수 | x      | 10     |
| 8진수  | O      | O12    |
| 16진수 | Ox     | OxA    |
| 2진수  | 0b     | 0b1010 |

### 부동소수점 리터럴

| 부동소수점 | 표기 | 예시                         |
| ---------- | ---- | ---------------------------- |
| float      | f    | 10.5f                        |
| double     | d    | 105.d                        |
| 지수       | e    | 2e4=>2.0 x 10 4승 => 20000.0 |

### 문자 리터럴

| 문자           | 표기          | 예시     |
| -------------- | ------------- | -------- |
| char           | ''            | 'A'      |
| char(유니코드) | '\u + unicode | '\u0041' |

### 문자열 리터럴

| 문자열 | 표기 | 예시  |
| ------ | ---- | ----- |
| String | ""   | "ABC" |

### 논리값 리터럴

| 논리값  | 표기         | 예시                 |
| ------- | ------------ | -------------------- |
| boolean | true / false | bollean bool = true; |

### null 리터럴

| null | 표기   | 예시            |
| ---- | ------ | --------------- |
| null | 값없음 | int var = null; |

## 스코프

**변수 유효범위**

1. 멤버 변수 스코프: 클래스 소속된 변수 => 클래스 전체에서 사용가능
2. 로컬 변수 스코프: 메서드, 컨스트럭터 소속된 변수 => 해당 메서드, 컨스트럭터에서만 사용가능 (즉, {} 안에서만)

```java
class A {
    int a = 10; 　　// 클래스에 소속된 멤버 변수

    public static void main(String args[]){
        int b = 100;　　 // 메서드에 소속된 로컬 변수
        A num = new A();
        num.show();
    }

    void show(){
        int c = 1000;  // 메서드에 소속된 로컬 변수
        System.out.println("a" + a + "이다.");
        System.out.println("b" + b + "이다.");
        System.out.println("c" + c + "이다.");
    }
}

// a 가능
// b 불가능 (컴파일 에러)
// c 가능
```

## 연산자

js랑 거의 같아서 넘어감

## 배열

```java
배열요소타입[] 배열변수명;
int[] numbers;

배열변수명 = new 배열요소타입[요소수];
numbers = new int[5];

배열요소타입[] 배열변수명 = new 배열요소타입[요소수]
int[] array = new int[5];
```

### 배열 초기값

| 타입                | 초기값 |
| ------------------- | ------ |
| byte,short,int,long | 0      |
| float,double        | 0.0    |
| boolean             | false  |
| char                | 0      |
| 객체타입            | null   |

```java
배열요소타입[] 배열 변수명 = {요소, 요소, ...};

int[] array = {1,10,100,1000,10000};

```

### 다차원배열

```java
int [][] array;

new int[3][3];

new String[][]{
    {"1-1","1-2","1-3"},
	{"2-1","2-2","2-3"},
	{"3-1","3-2","3-3"},
};
```

## 조건문, 반복문

JS 거의 같아 설명 생략

```java
if(){} else if(){} else{}

a?b:c;

switch(식){
    case1:
        break;
    case2:
        break;
    default:
        break;
}

```

```java

while(){}

do{}while()

for(int i=0;i<=5;i+-1>){}

// 확장for

for(요소타입 변수명: 배열명(컬랙션명)){
    처리;
}

int total = 0;
int number[] = {1, 2, 3, 4, 5};
for(int score: number){
　total += score;
}

break;
continue;
```

## 예외처리

- `try`, `catch`, `throw`, `finally` 사용
- Throwable: 모든 예외 객체 슈퍼클래스, 서브클래스는 Error, Exception
  - Error: 캐치하지않음(비체크 예외)
  - Exception: 서브클래스 포함 프로그램 내 발생한 에러, 체크해야함.
    - RuntimeException: 서브클래스 포함 프로그램 내 발생한 에러 중에서 회복이 불가한 것

```java
try {
    ...
} catch(예외1) {
    ...
} catch(예외2) {
    ...
...
} finally{
    ...
}
```

- 예외 생성도 가능
- `RuntimeExeption`과 `Exception`

| 예외 클래스                   | 체크여부 | 의미                                              |
| ----------------------------- | -------- | ------------------------------------------------- |
| Exception                     | ○        | 모든 예외의 슈퍼클래스                            |
| IOException                   | ○        | I/O 처리에서 에러 발생                            |
| SQLException                  | ○        | JDBC 에서 SQL 실행시 에러                         |
| RuntimeException              | ×        | 런타임 예외 슈퍼클래스                            |
| NullPointerException          | ×        | 인수가 null                                       |
| ClassCastException            | ×        | 자료형 캐스트 실패                                |
| IllegalArgumentException      | ×        | 인수에 적절히 못한 값이 전달 됨 (null에도 적용됨) |
| NumberFormatException         | ×        | 문자열이 숫자로 변환할 수 없을 때                 |
| ArithmeticException           | ×        | 이상한 계산 연산시에 (eg. 0으로 나누기)           |
| UnsupportedOperationException | ×        | 메서드 실행 서포트 되지 않을 때                   |
| UncatchedIOException          | ×        | catch 절에서 IOException 를 비체크 예외로 변환    |

### RuntimeExeption

```java
class FoolException extends RuntimeException {
}

public class Sample {
    public void sayNick(String nick) {
        if("fool".equals(nick)) {
            throw new FoolException();
        }
        System.out.println("당신의 별명은 "+nick+" 입니다.");
    }

    public static void main(String[] args) {
        Sample sample = new Sample();
        sample.sayNick("fool"); // => Exception in thread "main" FoolException
        sample.sayNick("genious");
    }
}
```

### Exception

```java

class FoolException extends Exception {
}

class void Sample{
try {
    ...
    } catch (FoolException e) {

    }
}
```

### with

- try-catch는 finally에 의해 받아줘야하는 것이 안전
- try-catch에서 with 사용하면 자동적으로 받히게끔 작성

#### 예시: 파일을 열 때

```java
public void readFile(Path path) throws IOException {
    BufferedReader reader = Files.newBufferedReader(path, Charset.forName("UTF-8"));
    try {
        String line = reader.readLine();
        while (line != null) {
            // 처리
            ...
            line = reader.readLine();
        }
    } catch (IOException e) {
        ...
        // IOException 다시 throw 다른 처리가 없으면 catch 자체를 생략가능
        throw e;
    } finally {
        reader.close();
    }
}
```

```java
public void readFile(Path path) throws IOException {
    // BufferedReader reader = Files.newBufferedReader(path) 열기
    // 열린 리소스는 try-catch 벗어날 때 자동적으로 받힘
    try (BufferedReader reader = Files.newBufferdReader(path, Charset.forName("UTF-8"))) {
        String line = reader.readLine();
        while (line != null) {
            // 처리
            ...
            line = reader.readLine();
        }
    } catch (IOException e) {
        ...
        // IOException 다시 throw 다른 처리가 없으면 catch 자체를 생략가능
        throw e;
    } // close() 가 필요없어져서 finally도 생략가능
}
```

### throws

```java
class FoolException extends Exception {
}

public class Sample {
    public void sayNick(String nick) throws FoolException {
        // 예외를 뒤로 미룰 수 있음.
        if("fool".equals(nick)) {
            throw new FoolException();
        }
        System.out.println("당신의 별명은 "+nick+" 입니다.");
    }

    public static void main(String[] args) {
        Sample sample = new Sample();
        try {
            sample.sayNick("fool");
            sample.sayNick("genious");
        } catch (FoolException e) {
            System.err.println("FoolException이 발생했습니다.");
        }
    }
}
```

### 예외할 때 피해야하는 것

- catch에서 아무것도 안하는 것
  - 원인파악이 불가능함
  - 애초에 예외 자체가 없던 것으로 취급됨

```java
try {
    ...
} catch (Exception e) {

}
```

- RuntimeException 남용 + 예외연쇄 미사용
  - 예외 추적이 어려워짐

```java
try {
    ...
} catch (IOException e) {
    // RuntimeException 남용 + 예외연쇄 미사용
    // 원인 예외가 없으면 에러 추적이 불가능함
    throw new RuntimeException();
}
```

- Throwalbe 캐치 또는 throw
  - `Exception`, `RuntimeExpection` 외에 `Error`까지 캐치하면 에러 파악이 어려워짐

```java
try {
    ...
} catch (Throwable t) {
    // 애초라면 캐치하면 안되는 Error를 캐치함
    // Error 는 Java VM 에러 등 심각한 상태를 표현하기 위함. 특별한 이유가 아니라면 캐치할 것이 아님
    ...
}
```

```java
// 호출처가 Throwable 를 캐치할 수 밖에 없는 상황이 만들어짐
public final void doProcess() throws Throwable {
    ...
}
```

## 쓰레드

- 동시성을 위함

1. Thread
2. Join
3. Runnable

## 람다

- js랑 같음

```js
(a, b) => a + b;
```

```java
(int a, int b) -> a + b;
```

## 스트림

- 말 그대로 데이터 흐름

- 이하의 정렬되지 않은 숫자 배열을 정렬한 뒤에, 2의 배수 중 10보다 큰 숫자만 남기고 싶을 때 `.` 첨자로 흐름을 정리한다.

```js
// js 예시
var arr = [12, 5, 6, 1, 2, 7, 20, 4, 6, 7, 2, 0, 6, 2, 1, 14];

arr
  .sort((x, y) => x - y)
  .filter((x) => x % 2 === 0)
  .filter((x) => x >= 10);

// [ 12, 14, 20 ]
```

- 자바도 이러한 동작을 할 수 있음

```java
import java.util.Arrays;
import java.util.Comparator;

public class Sample {
    public static void main(String[] args) {
        int[] data = {5, 6, 4, 2, 3, 1, 1, 2, 2, 4, 8};
        int[] result = Arrays.stream(data)  // IntStream을 생성한다.
                .boxed()  // IntStream을 Stream<Integer>로 변경한다.
                .filter((a) -> a % 2 == 0)  //  짝수만 걸러낸다.
                .distinct()  // 중복을 제거한다.
                .sorted(Comparator.reverseOrder())  // 역순으로 정렬한다.
                .mapToInt(Integer::intValue)  // Stream<Integer>를 IntStream으로 변경한다.
                .toArray()  // int[] 배열로 반환한다.
                ;
    }
}
```

- 람다와 스트림은 FP에서 영향은 받은 표현
- Java 또한 다른 패러다임의 장점을 받아들이고 있음.
