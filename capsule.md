# 캡슐화

## 결론

**은폐된 멤버를 다른 클래스에서 메서드를 통해서만 조작할 수 있도록 접근 제한하는 기능**

이를 위한 도구

- getter(은폐된 멤버 참조)
- setter(은폐된 멤버 갱신)

## 캡슐화

### 예시: 은행계좌

- 계좌명과 잔고확인은 본인이면 확인 가능
- 이를 참조하는 것(getter)
- 계좌명의 변경이나 잔고 갱신(출입금)
- 이를 갱신하는 것 (setter)

```java
public class BankAccount {
  // 계좌명
  private String myname;
  // 잔고
  private int balance;

  // 계좌명 참조
  public String getName() {
	return myname;
  }
  // 계좌명 갱신
  public void setName(String sVal) {
	myname = sVal;
  }
  // 잔고 참조
  public int getBalance() {
	return balance;
  }
  // 잔고 갱신
  public void setBalance(int val) {
	balance = val;
  }

  public void showAccount() {
	System.out.println("계좌명：" + myname);
	System.out.println("잔고：" + balance + "입니다.");
  }
}
```

```java
public class BankAccountMain {
  public static void main(String[] args) {
	BankAccount acc = new BankAccount();

	acc.setName("김라메");
	acc.setBalance(100000000);
	acc.showAccount();
  }
}
```

```bash
계좌명: 김라메
잔고: 100000000 입니다.
```

### 풀이

- myname, balance의 private
  - 이를 통해서 계좌명, 잔고를 멋대로 참조하거나 변경하지 못하도록 은폐
- getName(), getBalance()가 getter 역할로 메서드 참조
- setName(), setBalance()가 setter 역할로 메서드 갱신
- 기본적 캡슐화 구조는 멤버 변수에 private, 메서드에는 public를 붙임
