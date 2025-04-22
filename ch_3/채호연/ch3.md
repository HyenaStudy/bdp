# 디자인 패턴 학습 정리

설계 원칙: SOLID, KISS, YAGNI, LoD

---

## 1. SOLID 원칙

---

### 1.1 SRP (Single Responsibility Principle, 단일 책임 원칙)

#### 정의
- 클래스 또는 모듈이 오직 하나의 책임이나 기능만 수행하도록 설계하는 원칙
- 거대한 클래스(God Class)를 피하고, 각 책임별로 클래스와 모듈을 명확히 분리하여 설계한다.

> *열려있다* 와 *닫혀있다*란?
> - 열려있다란? : ‘그렇게 하는 것이 용이하다’, 즉 책임 혹은 기능 확장을 시도하는 경우 영향 범위를 최소화하며 확장 가능함을 의미
> - 닫혀있다란? : ‘기존 코드의 수정에 대해선 닫혀있다’, 즉 확장 시 이로 인한 영향 범위가 좁도록 설계되어, 기존의 구현 및 설계 변경을 최소화 혹은 없도록 할 수 있음을 의미한다.

#### 필요성
- 하나의 클래스가 여러 책임을 갖게 되면 유지보수와 이해가 어렵고, 변경 및 확장 시 영향 범위가 너무 커지게 된다.

#### 유의사항
- 모든 클래스를 과도하게 작은 단위로 분리하는 것도 좋지 않다.
- 책임의 적절한 범위는 기술적 관점뿐 아니라 비즈니스 관점에서도 판단해야 한다.

#### 예제 (Spring Boot 예시)

```java
// SRP가 적용되지 않은 클래스
public class User {
    private Long userId;
    private String username;
    private String email;
    private String province;
    private String city;
    private String region;
    private LocalDateTime createdAt;
}

// SRP가 적용된 설계 (주소를 별도 클래스로 분리)
@Entity
public class User {
    @Id
    private Long userId;
    private String username;
    private String email;
    private LocalDateTime createdAt;

    @Embedded
    private Address address;
}

@Embeddable
public class Address {
    private String province;
    private String city;
    private String region;
}
```

#### 생각해보기
> *클래스 설계 외에 단일 책임 원칙을 적용할 수 있는 설계에는 어떤 것들이 있는지 생각해보자.*

##### 나의 생각
> 보다 거시적인 관점에서 서비스를 설계 단계에서 도메인(문제 영역) 정의 시 단일 책임 원칙을 활용할 수 있을 것이라고 생각한다.
> 
> 사용자와 사용자의 포인트라는 개념이 존재할때, 사용자와 포인트 도메인을 분리하여 정의할 것인가, 혹은 하나의 도메인으로 정의할 것인가와 같은 문제들.

---

### 1.2 OCP (Open-Closed Principle, 개방-폐쇄 원칙)

#### 정의
- 소프트웨어는 확장에는 열려 있어야 하고, 기존 코드의 변경에는 닫혀 있어야 한다.

#### 개념
- 개방(Open): 새로운 기능 추가가 쉬워야 한다.
- 폐쇄(Closed): 기존 코드 수정 없이 확장할 수 있어야 한다.

#### 목적
- 요구사항 변화에 대응하기 쉬운 유연한 소프트웨어 설계를 추구한다.
- 변경 비용을 최소화하여 코드의 안정성을 유지한다.

#### OCP 설계 시 고려사항
- 확장 가능성이 있는 부분을 잘 식별하고 추상화하여 분리한다.
- 지나친 추상화로 복잡성을 초래하지 않도록 적절한 균형을 잡는다.

#### 예제 (Spring Boot 활용)

```java
public interface PaymentProcessor {
    void pay(int amount);
}

@Component
public class CreditCardProcessor implements PaymentProcessor {
    public void pay(int amount) {
        // 카드 결제 로직 구현
    }
}

@Component
public class KakaoPayProcessor implements PaymentProcessor {
    public void pay(int amount) {
        // 카카오페이 결제 로직 구현
    }
}

@Service
public class OrderService {
    private final PaymentProcessor paymentProcessor;

    public OrderService(PaymentProcessor paymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }

    public void processOrder(int amount) {
        paymentProcessor.pay(amount);
    }
}
```

#### 생각해보기
> *설계원칙을 배울 때 설계 원칙의 정의를 이해하는 것보다 훨씬 중요한 것은 ‘왜 그 원칙이 생겨날 수 밖에 없었는지’를 생각하여 목적을 파악하는 것. 여러분들의 생각은 무엇인가?*

##### 나의 생각
> 통상 시장 혹은 고객, 내부의 요구에 의해 설계 및 구현된 소프트웨어는 견고할 수 없다.
> 
> 이는 제품의 가치가 결정되는 시장의 요구 및 상황이 멈춰있지 않기 때문이다.
> 
> 그렇기 때문에 변경이 없을 것임을 생각하고 ‘완벽하게 설계 및 구현’하여 시장에 출시한 제품은 시장의 변화에 유연하게 대응을 할 수가 없게되고 결국 많은 노력을 들인 제품의 가치가 0에 수렴하는 일이 발생한다.
> 
> 이러한 상황을 고려하여, 제품이 시장의 변화 혹은 소비자의 필요에 따라 유연하게 확장 및 변화하고 성장해나가기 용이한 설계를 갖추도록 권고되는 원칙이 바로 OCP.


---

### 1.3 LSP (Liskov Substitution Principle, 리스코프 치환 원칙)

#### 정의
- 하위 클래스(자식 타입)는 언제든지 상위 클래스(부모 타입)를 대체할 수 있어야 하며, 그 결과 프로그램의 논리적 동작과 정확성은 그대로 유지되어야 한다.

#### 중요성
- 상속을 통한 확장에서 자식 클래스가 부모 클래스의 계약(행위)을 위반하지 않도록 보장한다.
- 다형성(polymorphism)을 안전하게 사용할 수 있도록 돕는다.

#### 다형성과의 차이
- 다형성: 하나의 심볼(유형)로 다양한 형태를 표현하는 개념.
- LSP: 부모 타입 자리에 자식 타입을 넣어도 프로그램 행위가 유지되는 성질.

#### 예제 (Spring Boot 환경)

```java
public abstract class NotificationService {
    abstract void notify(String message);
}

@Service
public class EmailNotificationService extends NotificationService {
    @Override
    void notify(String message) {
        // 이메일 전송 로직
    }
}

@Service
public class SmsNotificationService extends NotificationService {
    @Override
    void notify(String message) {
        // SMS 전송 로직
    }
}

// 어떤 자식 타입을 사용하든 동일한 결과 보장
@Service
public class NotificationManager {
    private final NotificationService notificationService;

    public NotificationManager(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    public void send(String message) {
        notificationService.notify(message);
    }
}
```
#### 생각해보기
> *리스코프 치환 원리의 중요성은 무엇인지 생각해보자.*
##### 나의 생각
> 반복되는 코드를 ‘부모 클래스’의 형태로 공통화하여 코드 반복을 줄임과 동시에, 당초 기획한 동작과 동일할 것을 보장하기 위함.
> 
> 또한, 최소한(반드시) 요구되는 사항을 충족하면서도 자식 클래스를 통해 동작 및 속성이 확장가능하도록 설계할 수 있음.
   
---

### 1.4 ISP (Interface Segregation Principle, 인터페이스 분리 원칙)

#### 정의
- 클라이언트는 사용하지 않는 메서드에 의존하면 안 된다.
- 큰 인터페이스를 잘게 나누어 특정 클라이언트의 요구에 맞는 인터페이스만 제공한다.

#### 필요성
- 인터페이스가 비대하면 사용하지 않는 기능까지 구현해야 하므로 불필요한 결합이 발생한다.
- 인터페이스를 작게 나누면 클라이언트의 요구사항 변경에 더욱 민첩하게 대응할 수 있다.

#### 예제 (Spring Boot 환경)

```java
// 잘못된 예: 지나치게 큰 인터페이스
public interface AccountService {
    void createAccount();
    void deleteAccount();
    void sendAccountEmail();
}

// 올바른 ISP 적용 예시
public interface AccountManagement {
    void createAccount();
    void deleteAccount();
}

public interface EmailNotification {
    void sendAccountEmail();
}
```

---

### 1.5 DIP (Dependency Inversion Principle, 의존 역전 원칙)

#### 정의
- 구체적인 클래스가 아닌 추상적인 인터페이스에 의존해야 한다.
- 상위 수준 모듈은 하위 수준 모듈에 의존하지 않고, 모두 인터페이스(추상화)에만 의존한다.

#### 목적
- 결합도를 낮추고, 코드의 유연성과 확장성을 높인다.
- 모듈의 변경이 다른 모듈에 전파되는 것을 방지한다.

#### 예제 (Spring Boot 환경)

```java
// DIP를 적용한 리포지토리 인터페이스
public interface UserRepository {
    User findById(Long id);
}

@Repository
public class JpaUserRepository implements UserRepository {
    public User findById(Long id) {
        // JPA로 User 조회 로직 구현
    }
}

@Service
public class UserService {
    private final UserRepository userRepository;

    // 인터페이스에 의존
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}
```

---

## 2. 기타 원칙 정리
- **KISS (Keep It Simple, Stupid)**
  - 복잡성을 최소화하여 단순하고 이해하기 쉬운 설계 지향.
- **YAGNI (You Ain't Gonna Need It)**
  - 당장 필요하지 않은 기능은 미리 구현하지 않는다.
- **LoD (Law of Demeter, 디미터 법칙)**
  - 객체 간의 불필요한 결합을 피하고, 객체가 다른 객체의 내부 구조에 의존하지 않도록 설계한다.
