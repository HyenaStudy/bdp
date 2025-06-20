# 디자인패턴 스터디 : Ch.7

## 7.0 구조 디자인패턴

### 정의
- 구조 디자인패턴은 특정 응용 프로그램 시나리오의 문제를 해결하기 위해 사용하는 클래스/객체의 고전적 구조를 정형화한 것.

### 종류

#### 1) 프록시 패턴 (Proxy Pattern)
- 원본 클래스와 관련이 없거나, 비즈니스 측면에서 주요 관심사가 아닌 기능을 추가하는 경우에 사용하는 구조.
- 접근 제어, 지연 로딩, 로깅, 캐싱 등의 부가 기능을 *원본 객체에 영향을 주지 않고* 추가할 수 있도록 함.

#### 2) 데커레이터 패턴 (Decorator Pattern)
- 원본 클래스와 관련이 있거나 향상된 기능을 기존의 클래스에 추가하는 경우 사용하는 구조
- 객체에 *동적으로 새로운 책임을 추가*할 수 있으며, 상속의 대안으로 기능을 확장할 수 있는 방안 제공

#### 3) 어댑터 패턴 (Adapter Pattern)
- 코드 호환성 문제를 해결하기 위해 주로 활용하는 구조

#### 4) 브리지 패턴 (Bridge Pattern)
- 상속의 폭발 문제를 해결하는데에 주로 사용되는 구조

#### 5) 파사드 패턴 (Facade Pattern)
- 인터페이스 설계에 사용하는 구조 
- 복수의 모듈이 복합적으로 얽혀 하나의 애플리케이션을 이루는 구조에서 *단일 진입점을 설계*할 때 자주 사용

#### 6) 복합체 패턴 (Composite Pattern)
- 트리 구조로 나타낼 수 있는 데이터 표현에 사용하는 구조

#### 7) 플라이웨이트 패턴 (Flyweight Pattern)
- 재사용 문제를 해결하는데에 주로 사용되는 구조

---

## 7.1 프록시 패턴

### 1. 프록시 패턴이란?
- 어떤 요청이 들어왔을 때, 해당 요청에 대한 응답 생성 및 반환을 책임지는 실제 객체를 대신하는(책임을 위임받은) 객체를 도입하는 패턴 
- 프록시 객체는 실제 객체와 동일한 인터페이스를 제공하면서, 실제 객체에 대한 접근을 제어하거나 부가적인 기능을 제공
- 이를 통해 클라이언트는 *실제 객체와 프록시 객체를 구분하지 않고 사용 가능*

**프록시 패턴의 주요 구성 요소:**
- **Subject**: 실제 객체와 프록시 객체가 공통으로 구현하는 인터페이스
- **RealSubject**: 실제 작업을 수행하는 객체
- **Proxy**: 실제 객체를 대신하여 클라이언트의 요청을 처리하는 객체
  
<img width="532" alt="Screenshot 2025-06-17 at 8 44 26 PM" src="https://github.com/user-attachments/assets/6c7f4b1b-a5d8-4568-b745-72f7adbce246" />


### 2. 프록시 패턴을 구현하는 대표적인 방법

#### 1) 인터페이스 기반의 프록시 패턴
**장점:**
- 인터페이스를 통해 실제 객체와 프록시 객체 간의 결합도가 낮음
- 다형성을 활용하여 런타임에 실제 객체와 프록시 객체를 교체할 수 있음
- 인터페이스 계약을 통해 일관된 동작을 보장
- 테스트하기 용이함 (Mock 객체 사용 가능)

**단점:**
- 인터페이스를 별도로 정의해야 하므로 코드량이 증가
- 인터페이스가 변경되면 모든 구현체를 수정해야 함
- 인터페이스에 정의된 메서드만 프록시할 수 있음

#### 2) 상속 기반의 프록시 패턴
**장점:**
- 인터페이스를 별도로 정의할 필요가 없음
- 기존 클래스의 모든 메서드를 자동으로 상속받음
- 구현이 상대적으로 간단함

**단점:**
- 상속을 사용하므로 결합도가 높음
- final 클래스나 final 메서드는 상속할 수 없음
- 런타임에 프록시 객체를 교체하기 어려움
- 다중 상속이 불가능한 언어에서는 제약이 있음

#### 3) 리플렉션 기반의 동적 프록시

### 3. 프록시 패턴의 활용 방안

#### 1) 주요 비즈니스와 관련 없는 요구 사항의 개발에 활용될 수 있다
- 프록시 패턴은 횡단 관심사(Cross-cutting Concerns)를 처리하는 데 매우 유용

- **로깅**: 메서드 호출 전후에 로그를 남김
- **보안**: 접근 권한을 확인하고 인증/인가 처리
- **성능 모니터링**: 메서드 실행 시간 측정
- **트랜잭션 관리**: 메서드 실행 전후에 트랜잭션 시작/종료
- **캐싱**

### 4. 생각해보기

#### Java 외의 다른 프로그래밍 언어는 동적 프록시를 어떻게 구현하는지 살펴보자

**Python:**
```python
class LoggingProxy:
    def __init__(self, target):
        self._target = target
    
    def __getattr__(self, name):
        attr = getattr(self._target, name)
        if callable(attr):
            def wrapper(*args, **kwargs):
                print(f"메서드 호출: {name}")
                result = attr(*args, **kwargs)
                print(f"메서드 완료: {name}")
                return result
            return wrapper
        return attr
```

**JavaScript:**
```javascript
const createProxy = (target) => {
    return new Proxy(target, {
        get(target, prop, receiver) {
            const originalMethod = target[prop];
            if (typeof originalMethod === 'function') {
                return function(...args) {
                    console.log(`메서드 호출: ${prop}`);
                    const result = originalMethod.apply(this, args);
                    console.log(`메서드 완료: ${prop}`);
                    return result;
                };
            }
            return originalMethod;
        }
    });
};
```

#### 인터페이스 기반의 프록시 패턴과 상속 기반의 프록시 패턴의 장단점을 비교해보자

| 구분 | 인터페이스 기반 | 상속 기반 |
|------|----------------|-----------|
| **결합도** | 낮음 (인터페이스를 통한 느슨한 결합) | 높음 (상속을 통한 강한 결합) |
| **유연성** | 높음 (런타임 교체 가능) | 낮음 (컴파일 타임 고정) |
| **테스트** | 용이함 (Mock 객체 사용 가능) | 어려움 (상속 관계 의존) |
| **구현 복잡도** | 높음 (인터페이스 정의 필요) | 낮음 (기존 클래스 상속만) |
| **제약사항** | 인터페이스 메서드만 프록시 가능 | final 클래스/메서드 불가 |
| **확장성** | 높음 (다양한 구현체 가능) | 낮음 (단일 상속 제약) |

### 5. 특집: Spring의 @Transactional - 프록시 패턴을 어떻게, 왜 사용할까?

Spring Framework의 `@Transactional` 어노테이션은 프록시 패턴을 활용한 대표적인 예시. 한 번 가봅시다!

#### 동작 원리
아래와 같은 코드가 있다고 가정해볼게요.

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Transactional
    public void createUser(String name, String email) {
        User user = new User(name, email);
        userRepository.save(user);
        
        // 만약 여기서 예외가 발생하면 롤백됨
        if (email.contains("invalid")) {
            throw new RuntimeException("Invalid email");
        }
    }
}
```

Spring은 `@Transactional`이 붙은 클래스에 대해 프록시 객체를 생성

```java
// Spring이 내부적으로 생성하는 프록시 (개념적 구현)
public class UserServiceProxy extends UserService {
    
    private PlatformTransactionManager transactionManager;
    
    @Override
    public void createUser(String name, String email) {
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
        
        try {
            super.createUser(name, email);  // 실제 비즈니스 로직 실행
            transactionManager.commit(status);
        } catch (Exception e) {
            transactionManager.rollback(status);
            throw e;
        }
    }
}
```

#### 왜 프록시 패턴을 사용하는가?

1. **비즈니스 로직과 기술적 관심사의 분리**: 트랜잭션 관리는 비즈니스 로직과 별개의 기술적 관심사입니다.

2. **코드의 재사용성**: 트랜잭션 관리 코드를 매번 작성할 필요가 없습니다.

3. **선언적 프로그래밍**: 어노테이션만으로 트랜잭션 동작을 정의할 수 있습니다.

4. **투명성**: 개발자는 트랜잭션 관리 코드를 직접 작성하지 않아도 됩니다.

#### Spring의 프록시 생성 방식

Spring은 두 가지 방식으로 프록시를 생성합니다:

1. **JDK Dynamic Proxy**: 인터페이스 기반
2. **CGLIB Proxy**: 상속 기반 (클래스 기반)

```java
// 설정을 통한 프록시 방식 선택
@EnableTransactionManagement(proxyTargetClass = true) // CGLIB 사용
public class AppConfig {
    // 설정 내용
}
```

---

## 7.2 데커레이터 패턴: 합성과 상속 간 차이를 중심으로

### 1. 데커레이터 패턴이란?

- 데커레이터 패턴은 객체에 동적으로 새로운 기능을 추가할 수 있게 해주는 구조적 디자인 패턴
- 기존 객체를 수정하지 않고도 기능을 확장할 수 있으며, 상속의 대안으로 사용

**핵심 특징:**
- 객체를 감싸는(wrapping) 방식으로 동작
- 런타임에 동적으로 기능 추가/제거 가능
- 여러 데커레이터를 중첩하여 복합적인 기능 구현 가능
- 원본 객체의 인터페이스를 그대로 유지

**구성 요소:**
- **Component**: 기본 기능을 정의하는 인터페이스
- **ConcreteComponent**: 기본 기능을 구현하는 클래스
- **Decorator**: 데커레이터의 기본 클래스
- **ConcreteDecorator**: 구체적인 기능을 추가하는 데커레이터
