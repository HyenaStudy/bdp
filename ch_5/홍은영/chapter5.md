## 리팩터링
- 소프트웨어 동작은 유지하면서 내부 구조를 이해하기 쉽게 개선하는 작업
- 수정 및 기능 추가 시 비용감소
- 코드 품질 저하 방지
<br>

### 리팩터링 대상
- 대규모
  - 시스템, 모듈, 코드 구조, 클래스 간 관계 등 상위 수준의 코드
  - 계층화, 모듈화, 분리, 추상화 등 사용

- 소규모
  - 클래스, 함수, 변수 수준에서의 코드 세부 정보
  - 중복 코드 제거, 표준 명명 사용 등
<br>

### 결론
- 상세하고 명확한 계획 설계
- 기능을 변경하지 않고 항상 실행 가능한 상태를 유지하면서 단계적으로 수행
- 다른 기능이나 새로운 기능과 충돌나면 안됨
<br><br><br>


## 단위 테스트
- 클래스나 함수 수준에서의 코드 테스트
- 예상대로 실행되는지 확인
- 정상, 비정상 상황을 포괄하는 테스트 케이스 설계
- 간단한 코드는 굳이 필요 X
<br>

### 테스트 코드 작성을 위한 리팩토링 방법
#### 1) 의존성 주입
- 모의 객체를 만든 후 테스트 대상 클래스에 주입해서 사용
```java
// 재정의 사용해서 모의구현 클래스 정의
public class Mock1 extends WalletRpcService {
  public String moveMoney ~~~
}

public class Mock2 extends WalletRpcService {
  public String moveMoney ~~~
}

// 의존성 주입을 위한 setter 정의
public class Transaction {
  private WalletRpcService wallet;

  public void setterWallet~~
}

// 테스트케이스
public void test() {
  Transaction transaction = new ~~
  transaction.setterWallet(new Mock1);
}
```
<br>

#### 2) 캡슐화
- 외부 시스템을 캡슐화한 후 모의 객체로 대체
```java
// 캡슐화 사용해서 모의구현 클래스 정의
public class TransactionLock {
  public boolean lock {
    return RedisDistributedLock.lock~~~
  }
  public boolean unlock {
    RedisDistributedLock.unlock~~~
  }
}

// 모의구현 클래스 사용 예시?
private TransactionLock lock;
~~ lock.lock;
```
<br>

#### 3) setter 메서드가 없을 때
- setter를 추가하는 것은 캡슐화 특성 위배
- 주로 시간에 따라 흐름과 결과가 영향을 받는 코드  
→ 해당 동작을 패키징해서 캡슐화
<br><br>

## 디커플링
- 높은 응집도, 낮은 결합도 코드 생성  
  → 코드의 복잡성 제어에 효과적  
  → 명확한 구조, 합리적인 계층화와 모듈화, 간단한 의존성

- 코드를 일부만 수정하는데 전체 코드를 건드려야 하는가??  
  → 결함도가 높아서 디커플링 필요