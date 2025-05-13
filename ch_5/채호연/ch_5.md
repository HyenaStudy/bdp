# 디자인패턴 스터디 : Ch5_리팩터링 기법

## I. 리팩터링이란?

### 1. 리팩터링 : 정의
```
코드에 대한 이해가 쉽도록 소프트웨어 내부 구조를 개선하는 것으로, 소프트웨어의 외부 동작을 변경하지 않고 수정 비용을 줄이는 것을 목적으로 한다.
- Martin Fowler
```

### 2. 리팩터링 : 효익
- 고품질 코드는 완벽한 단 한 번의 설계의 결과가 아니라 반복적인 작업의 결과
  - 미래의 요구사항 변경을 설계 시점에 완벽하게 예측할 수 없기 때문
- 이러한 맥락 하에 리팩터링은 아래의 효익을 가져다 준다:
  1) 코드 품질 저하를 방지함으로써 고품질을 약속할 수 있도록 하는 효과적인 수단
  2) 주기적인 리팩터를 통해 오버 엔지니어링을 방지할 수 있다
  3) 소프트웨어 엔지니어의 기술적 성장을 위한 기여: 설계 원칙과 디자인 패턴을 응용해볼 수 있는 좋은 기회를 제공하기 때문
- 리팩터링은 '개발 단계의 일부'로 취급하여 '지속적인 리팩터링'을 해야 한다
- 리팩터링은 크게 다음의 두 가지 개발자 역량이 필요한 영역이다:
  1) 통찰력: 코드 스멜, 설계 결함을 감지할 수 있는 통찰력을 의미한다. 일반적으로 개발자로서의 경험 밀도가 이러한 통찰력의 수준과 밀접한 관계를 갖는다.
  2) 이해: 설계 원칙, 디자인 패턴, 코딩 규칙에 대한 이해 자체를 의미한다.

### 3. 리팩터링 : 대상
- **대규모 고수준 리팩터링(aka 대규모 리팩터링)**
  - 시스템, 모듈, 코드 구조, 클래스 간 관계를 포함하여 최상위 코드 설계를 리팩터링하는 것
  - 대규모 리팩터링은 크게 다음의 방식을 활용한다:
    - 계층화
    - 모듈화
    - 분리
    - 재사용 가능한 구성요소 추상화
  - 코드 변경이 많고 영향이 크기 때문에 난이도가 높고 시간이 오래 걸리는 것이 일반적

- **소규모 저수준 리팩터링(aka 소규모 리팩터링)**
  - 표준 명명 표준 주석, 초대형 클래스 및 함수 제거, 중복 코드 추출과 같이, 주로 클래스/함수/변수 수준에서의 리팩터링을 의미
  - 주로 4장에서 다루었던 '코딩 규칙'을 활용

### 4. 리팩터링 : 방법
- 대규모 리팩터링의 경우, 변경으로부터 영향 전파 범위가 넓기 때문에 사전에 이를 고려한 견고한 계획을 세워 진행해야 한다.
- 소규모 리팩터링의 경우 변경으로 인한 영향 전파 범위가 통제 가능한 수준이기에 시간이 있을 때마다 가능하다. 이때 정적 코드 분석 도구(ex. Sonarqube)를 활용할 수 있다.

## II. 단위 테스트

### 1. 단위 테스트 : 정의
- 리팩터링과 같은 수정 및 보수 작업을 수행할 때 항상 염두에 두어야 하는 것은, 변경 후에도 '이전과 같이 의도대로 작동하는지 확인하는 것'이다.
- 그렇기 때문에 리팩터링에서 빼놓을 수 없는 것이 단위 테스트이다.
- 일반적으로 클래스 또는 함수 대상으로 제한된 범위를 테스트 대상으로 한다.
- 통상 테스트 케이스 설계 후 이를 코드로 변환하는 과정을 통해 작성된다.

### 2. 단위 테스트 : 목적 및 효익
1) 코드 수준의 버그 혹은 설계 결함을 찾는데에 유용하다.
2) 통합테스트를 보완하는 강력한 도구이다.
   - 통합 테스트를 통해 검증이 어려운 예외 상황이 존재한다. 이를 단위 테스트에서 Mocking 혹은 Stubbing을 활용해 재현하여 테스트할 수 있다.
3) 단위 테스트 작성 자체가 리팩터링의 한 과정에 해당한다.
   - 단위 테스트 코드를 작성은 테스트 용이성 및 설계 상의 문제에 대한 점검 과정을 수반한다.
4) 개발자가 코드에 빠르게 익숙해지도록 도와준다.
   - 단위 테스트 자체가 문서 및 주석 역할을 수행할 수 있기 때문이다.

### 3. 단위테스트에 대한 이모저모
- **단위 테스트를 설계하는 것은 시간이 많이 걸리는 일인가?**
  - 때때론 테스트 코드가 테스트하고자하는 클래스/메서드 코드보다 길거나 많은 경우가 있다.
  - 하지만 단위 테스트 코드 자체는 구현이 간단하고 설계를 고려할 필요가 없기에, 작성에 예상만큼 긴 시간이 걸리지 않는다.
  - 또한 테스트 케이스에 따라 하나의 테스트 코드를 재사용할 수 있는 경우가 많다.

- **단위 테스트 코드의 품질에 대한 요구사항이 있는가?**
  - 단위 테스트 코드의 경우 서로 독립적이므로(테스트 코드 간 의존이 존재하지 않으므로), 통상적인 프로덕션 환경에서의 코드에 요구하는 수준보다 낮은 수준을 적용할 수 있다.

- **테스트 커버리지가 높으면 좋은 테스트인가?**
  - 그렇지 않다. 테스트 커버리지보단 발생 가능한 상황을 꼼꼼하게 고려한 테스트 시나리오가 더 중요하다.
  - 예외 및 경계 상황에 대한 테스트까지 꼼꼼하게 작성했다면, 커버리지가 매우 높지 않더라도 충분히 잘 작성된 테스트라고 할 수 있다.

- **테스트 코드 작성 시 코드 구현 논리를 이해하는 것이 필요한가?**
  - 꼭 그렇지 않다. 오히려, 구현 논리보다 기능 자체에 초점을 맞춘 테스트가 더 적절하다고 할 수 있다.
  - 구현 논리에 의존적인 테스트는, 이후 요구사항 변경에 따라 구현 내용이 달라질 경우 테스트가 실패할 수 있기 때문.

## III. 테스트 용이성

### 1. 예제 코드 설명
- `Transaction.execute()`: 송금 작업을 통해 구매자의 지갑 -> 판매자의 지갑으로 돈을 이체한다.
- 이때 송금 자체는 `WalletRpcService`라는 이름의 RPC 서비스를 호출하여 수행한다.
- 동시성 제어를 위해 `RedisDistributedLock` 싱글턴 클래스를 통해 분산 락을 적용한다. 흔히 말하는 따닥 방지이다!

### 2. 예제 코드
```java
public class Transaction {
    private String id;
    private Long buyerId;
    private Long sellerId;
    private Long productId;
    private String orderId;
    private Long createTimestamp;
    private Double amount;
    private STATUS status;
    private String walletTransaction;

    public Transaction(String preAssignedId, Long buyerId, Long sellerId, Long productId, String orderId) {
        if (preAssignedId != null && !preAssignedId.isEmpty()) {
            this.id = preAssignedId;
        } else {
            this.id = IdGenerator.generateTransactionId();
        }
        
        if (!this.id.startsWith("t_")) {
            this.id = "t_" + preAssignedId;
        }
        
        // 필드 생성자 주입 과정 생략.
    }

    // 실제 로직
    public boolean execute() throws InvalidTransactionException {
    
        // Case I: 올바르지 않은 필드 값의 경우 예외 발생    
        if (buyerId == null || sellerId == null || amount < 0.0) {
            throw new InvalidTransactionException();
        } 
        // Case II: A이미 실행된 Transaction인 경우 true 반환.
        if (status == STATUS.EXECUTED) {
            return true;
        }

        boolean isLocked = false;

        // RedisDistributedLock 싱글턴 객체를 통해, 해당 Transaction ID에 대한 처리가 이미 진행 중인지 확인함으로써, 중복 처리 방지.
        try {
            isLocked = RedisDistributedLock.getSingletonInstance().lockTransaction(id);
            if (!isLocked) {
                return false;
            }
            if (status == STATUS.EXECUTED) {
                return true;
            }

            long executionInvokedTimestamp = System.currentTimestamp();
            if (executionInvokedTimestamp - createTimestamp > 14days) {
                this.status = STATUS.EXPIRED;
                return false;
            }

            // 중복 문제가 없도록 락 획득 후 송금 로직 수행
            WalletRpcService walletRpcService = new WalletRpcService();
            String walletTransactionId = walletRpcService.moveMoney(id, buyerId, sellerId, amount);
            
            if (walletTransactionId != null) {
                this.walletTransactionId = walletTransactionId;
                this.status = STATUS.EXECUTED;
                return true;
            } else {
                this.status = STATUS.FAILED;
                return false;
            }
        }

        // 송금 로직 성공적으로 수행 완료 후 락 해제.
        finally {
            if (isLocked) {
                RedisDistributedLock.getSingletonInstance().unlockTransaction(id);
            }
        }
    }
}
```

### 3. 테스트 시나리오 설계
1) 트랜잭션 성공적으로 실행 시, 트랜잭션과 지갑 간의 트랜잭션 흐름 조정 과정에서 사용된 식별자(walletTransactionId) 필드가 채워진다. 상태는 EXECUTED. true 반환.
2) 비정상적인 송금(buyerId == null || sellerId == null || amount < 0.0): InvalidTransactionException 예외를 발생시킨다.
3) 트랜잭션 실행 시점을 기준으로 createTimeStamp로부터 14일 이상 지난 경우, 트랜잭션을 만료 처리한다. status == EXPIRED. false 반환.
4) 트랜잭션이 이미 처리된 경우(status == EXECUTED) true를 반환하고 execute() 실행을 종료한다.
5) WalletRpcService 호출에 실패하여 전송이 실패한 경우, 트랜잭션 상태를 FAILED로 변경하고 false를 반환하고 종료한다.
6) 아직 트랜잭션이 처리 중인 경우(다른 스레드 혹은 서버 인스턴스에서 해당 트랜잭션 요청 처리 중), false를 반환하고 종료.

이제 이 시나리오들을 코드로 변경하며, 우리가 살펴볼 예제 코드가 '테스트 용이성'을 갖추었는지 확인하고, 테스트가 어려운 경우를 식별하며 이때 적절한 조치 방안이 무엇인지 학습하는 것이 목표이다.

### 4. 외부 서비스 호출을 포함한 메서드에 대한 테스트: Test Double
- 1번 시나리오를 테스트 하기 위한 코드를 작성해보자.
```java
public void testExecute() {
    Long buyerId = 123L;
    Long sellerId = 234L;
    Long productId = 345L;
    String orderId = "456";
    Transaction transaction = new Transaction(null, buyerId, sellerId, productId, orderId);
    boolean executedResult = transaction.execute();
    assertTrue(executedResult);
}
```

- 하지만 이때 `execute()` 함수는 두 개의 외부 서비스(`RedisDistributedLock`, `WalletRpcService`)에 의존하고 있다. 이는 테스트 시 다음과 같은 문제를 발생시킨다:
  - 단위 테스트 실행을 위해 두 가지 외부 서비스를 모두 구축하고 실행하여 접근 가능한 상태로 만들어두어야한다. 이는 리소스 비용도 클 뿐 더러, 외부 서비스 상태에 따라 코어 비즈니스 로직의 테스트 결과가 일관적이지 않게 된다는 문제를 발생시킨다.
  - 외부 서비스가 통제 불가한 경우(ex. 외부 업체의 서비스를 활용하는 경우), 반환될 값을 예상 혹은 확정하지 못해 테스트가 어렵다는 문제가 존재한다.
  - 또한, 외부 서비스와의 통신이 네트워크를 통해 이루어지고 응답을 받아야만 함수 실행의 남은 과정이 처리될 수 있으므로, 테스트 수행 시간 자체가 길어진다는 문제 또한 존재한다.

- 이를 해결하기 위한 것이 바로 모의 구현(Test Double)이다. 

#### 1) WalletRpcService 모의 객체 도입: 테스트 용이성 문제 발견 및 해결하기
- 외부 서비스인 WalletRpcService의 역할을 대체할 수 있고 우리가 예상한 값이 반환되도록 동작을 조정할 수 있는 방법이 필요한 상황.
- 그렇기 때문에 다음과 같은 Mock 객체를 정의한다.
```java
public class MockWalletRpcServiceForValidTestCase extends WalletRpcService {
    public String moveMoney(Long id, Long fromUserId, Long toUserId, Double amount) {
        return "123abc";
    }
}

public class MockWalletRpcServiceForInvalidTestCase extends WalletRpcService {
    public String moveMoney(Long id, Long fromUserId, Long toUserId, Double amount) {
        return null;
    }
}
```

- 이제 대체만 하면 된다고 생각하였으나, 문제를 발견: `execute()` 코드 실행 시 WalletRpcService 객체를 new 키워드를 통해 동적으로 생성하는 방식으로 작동하는 것.
- 바로 이러한 부분이 테스트가 용이하지 않은 것.
- 이를 해결하기 위해 Transaction 클래스를 다음과 같이 수정한다:
```java
public class Transaction {
    // ...
    private WalletRpcService walletRpcService; // WalletRpcService를 주입받을 수 있도록 필드 정의

    public void setWalletRpcService(WalletRpcService walletRpcService) {
        this.walletRpcService = walletRpcService;
    }

    public boolean execute() {
        // ...
        // 아래 코드 삭제
        // WalletRpcService walletRpcService = new WalletRpcService();
    }
}
```

#### 2) RedisDistributedLock: 캡슐화를 통한 테스트 용이성 재고
- 위의 WalletRpcService와 마찬가지로 외부 서비스이나, 싱글톤 객체로 접근하는 형태로 활용된다는 점과 더불어 해당 클래스를 직접 수정할 수 없는 상황(가정)이라면 아래와 같은 캡슐화를 통해 테스트 용이성을 재고해볼 수 있다.

```java
// 아래와 같이 데이터와 이에 대한 행위 자체를 하나로 묶는 '캡슐화'를 진행한다.
// 클래스를 통해 캡슐화하였으므로, 이를 활용하는 execute() 메서드에 대한 테스트를 작성 시 분산 락 관련 로직의 세부 구현에 의존할 필요가 없어지게 된다.
// 물론 캡슐화 자체만으로 테스트 용이성 문제가 해결되는 것이 아니다.
// 클래스 형태로 캡슐화를 함으로써 'Test double'이 사용 가능하게 된다는 점이 핵심이다.
public class TransactionLock {
    public boolean lock(String id) {
        return RedisDistributedLock.getSingletonInstance().lockTransaction(id);
    }
    public void unlock(String id) {
        RedisDistributedLock.getSingletonInstance().unlockTransaction(id);
    }
}

public class Transaction {
    private TransactionLock lock;
    
    public void setTransactionLock(TransactionLock lock) {
        this.lock = lock;
    }

    public boolean execute() {
        // ...
        try {
            isLocked = lock.lock(id);
            // ...
        } finally {
            if (isLocked) {
                lock.unlock(id);
            }
        }
    }
}
```

이렇게 리팩터할 경우 다음과 같이 테스트 코드를 작성할 수 있게 된다.
```java
public void testExecute() {
    Long buyerId = 123L;
    Long sellerId = 234L;
    Long productId = 345L;
    String orderId = "456";

    TransactionLock mockLock = new TransactionLock() {
        public boolean lock(String id) {
            return true;
        }
        public void unlock(String id) {}
    };
    
    // 테스트 코드 계속...
}
```

### 5. 테스트가 불가능한 코드
#### 1) 보류 중인 동작
- 코드의 출력이 무작위이거나 불확실하며, 대부분 시간 및 난수와 관련이 있는 경우를 의미함.
- 테스트가 불가능한 이유는 입력과 출력을 완전히 통제할 수 없기 때문이다.
- 예시 코드
```java
public class Demo {
    public long calculateDelayDays(Date dueTime) {
        long currentTimestamp = System.currentTimeMillis(); // 바로 여기가 문제이다.
        // ...
    }
}
```

#### 2) 전역 변수
- 전역 변수를 클래스 내 여러 메서드에서 공유하는 경우(이게 일반적인 상황이긴 하지만) 테스트 코드 간 독립성을 해칠 수 있다.

#### 3) 정적메서드
- 정적 메서드의 경우 모의 구현이 어렵기 때문에 테스트 용이성이 떨어질 수 있다.
- 반드시 이에 대한 테스트가 이루어져야 하는 경우(ex. 실행 시간이 너무 길거나, 외부 리소스에 의존하거나 등), 이를 Mocking 함으로써 단위테스트를 진행해야 한다.

#### 4) 복잡한 상속관계
- 합성 관계보다 상속 관계의 결합성이 훨씬 높다.
- 상위 클래스에서 단위 테스트를 위해 종속 객체를 모의 구현해야 하는 경우 이 상위 클래스를 상속받는 모든 하위 클래스의 단위 테스트 코드에서 이 종속 객체를 모두 모의 구현해야 하는 상황이 발생할 수 있다.

### 6. 생각해보기
#### 1) IdGenerator.generateTransactionId() 라는 정적 함수를 execute() 메서드 내부에서 호출한다고 가정해보자. 이것이 테스트 용이성에 영향을 미치는가? 또한 단위 테스트를 작성할 때 이 메서드를 모의 구현해야하는가?
- IdGenerator.generateTransactionId()의 작동 방식에 따라 달라진다. 만약 모의 구현을 통해 ID 생성과정이 일관적이도록 구현할 수 있다면 테스트 용이성에 영향을 미치지 않는다. 
- 하지만 UUID v4 혹은 UNIX Timestamp와 같이 생성 시점에 따라 값이 달라지는 방식으로 ID를 생성한 경우 테스트 하기가 굉장히 어려워진다.
- 이를 어떻게 해결하는가? -> Interfacing + Mocking.

#### 2) 의존성 주입은 new 예약어를 통해 클래스 내부에서 객체를 생성하는 것이 아니라 외부에서 객체를 생성한 후 클래스에 전달하는 것을 의미한다. 그렇다면 모든 객체를 클래스 외부에서 생성해야 하는 것이 옳은 지를 생각해보자. 또한 코드의 테스트 용이성에 영향을 주지 않고 클래스 내에서 생성할 수 있는 유형의 객체에는 어떤 것이 있는지 생각해보자.
- 이건 잘 모르겠다. 다른 사람의 의견이 궁금하다.

## IV. 디커플링

### 1. 디커플링이란?
- 디커플링의 목적: 높은 응집도와 낮은 결합도를 가진 소프트웨어로의 개선
  - 디커플링을 통한 '높은 응집도, 낮은 결합도'로의 개선은 크게 두 가지 장점을 갖는다:
    - 어떤 하나의 기능 단위를 이해하기 위해 이와 무관한 다른 모듈 혹은 기능 단위를 이해할 필요가 적어진다. 따라서 코드 유지 보수성을 제고할 수 있다.
    - 의존 관계가 명료하고 결합도가 낮으므로, 코드 수정으로 인한 영향 전파 범위가 통제 가능한 수준에 머무르도록 할 수 있다.
- 대규모 리팩터링은 디커플링을 주 목표로 하는 것이 일반적이다.

### 2. 디커플링: 대표적인 방법들
#### 1) 캡슐화와 추상화로 디커플링하기
- 구현 세부사항 및 복잡도를 숨기고, 구현 변경 시 영향 전파 범위를 격리할 수 있다.

#### 2) 중간계층으로 디커플링하기
- 중간계층을 도입함으로써 모듈 및 클래스 간 의존 관계를 단순화할 수 있다.
- 대표적인 예로 복수의 RDBMS 제품을 모두 사용해야 하는 경우(MySQL, PostgreSQL), Repository 계층을 Interface로 정의하고 이에 대한 각 RDBMS 별 구현체를 제공하는 설계가 있다. 이 경우 Repository 인터페이스가 바로 중간계층의 역할을 한다고 볼 수 있는 것이다.

#### 3) 모듈화와 계층화로 디커플링하기
- **모듈화**: 전체 시스템을 관심사 및 문제 영역(Domain)에 따라 여러 개의 독립적인 서브 시스템의 형태로 분리하여 설계하는 방식
  - ex. UNIX 시스템은 프로세스 스케쥴링, 프로세스 통신, 메모리 관리, 가상 파일 시스템, 네트워크 인터페이스 모듈 등으로 분리된 구조로 설계되었다.
- **계층화**: 시스템을 수직적으로 분할된 구조로 분리하여 설계하는 것. 각 계층 간 협업은 인터페이싱을 통해 이루어지도록 설계하는 것이 바람직하다. 이를 통해 구현 세부 사항을 숨기고 변경 전파 범위를 통제하여 유지보수성을 제고할 수 있기 때문이다.
  - ex. UNIX 시스템은 크게 커널, 시스템 호출, 응용 프로그램 계층으로 수직적으로 분리된 구조를 갖도록 설계되었다.

### 3. 고전적인 코드 설계 원칙과 사상을 활용한 디커플링
#### 1) 단일 책임 원칙
- 응집도와 결합도는 서로 완전히 독립적인 요소가 아니기 때문에, 응집도가 높으면 통상 코드의 결합도가 낮아진다.
- 이때 단일 책임 원칙은 '높은 응집도'를 달성하기 위한 지침이므로, 이를 고려한 설계 시 낮은 결합도도 상당 수준 성취 가능하다.

#### 2) 구현이 아닌 인터페이스 기반의 프로그래밍

#### 3) 의존성 주입
- 의존성 주입을 통해 모듈 또는 클래스 사이의 강한 결합을 약한 결합으로 바꿀 수 있다.

#### 4) 상속 보다는 합성을 더 많이 사용하기
- 상속은 앞서 기술하였듯 강한 의존 관계에 해당한다.
- 따라서, 복잡한 상속 관계를 합성 관계로 대체하는 방식으로 디커플링을 달성할 수 있다.

#### 5) LoD(Law of Demeter)를 따르는 것
- 최소 지식의 원칙으로, 객체는 자신이 조작하는 객체의 내부 구조에 대해 최소한의 지식만 가져야 한다는 원칙이다.
- 이 원칙을 따를 경우 클래스 간의 결합도를 낮추는 효과를 얻을 수 있다.
