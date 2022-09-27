# 패키지

클래스 파일이 여럿이 생기며, 이를 정리하는 것 => **패키지**

## 패키지화

```java
package 패키지명;
package a.b.c.d;
```

## 패키지화 한 클래스 컴파일 실행

```java
$ javac 패키지명/파일명;   // Mac
$ javac 패키지명\파일명;   // Windows
```

```bash
$ java 패키지명/클래스명;  //Mac
$ java 패키지명/.클래스명;  //Mac
$ java 패키지명\\클래스명;  //Windows
$ java 패키지명.클래스명;  //Windows
```

## import

```java
import 클래스명;

import A.B;

import A.*; // 컴파일에러

package A;
import A.B.*
```

임포트되려면 이하의 규칙을 따름

- 클래스 수식자 public
- 사용하는 클래스 멤버 수식자 public
