# 디자인패턴 Ch.8 행동 디자인 패턴

> **행동 디자인 패턴**
> - 클래스 및 객체 간 상호 작용 문제를 해결하기 위해 고안된 패턴

## I. 옵서버 패턴(Observer Pattern)

### 1. 정의

"많은 객체들 사이에서 일대일 의존 관계가 정의되어 있을 때, 어느 한 객체의 상태가 변경되면 이 객체에 의존하고 있는 모든 객체는 자동으로 알림을 받는다." - <GoF의 디자인 패턴>

- 발행-구독 패턴(publish-subscribe pattern) 이라고 하기도 함.
- 해당 패턴에 참여하는 객체는 크게 두 종류로 분류한다.
    - **옵서버블(Observable)** : 피관찰자. 상태 변경을 알리는 쪽. Subject, Publisher, Producer, EventEmitter, Dispatcher 등으로 불리기도 한다.
    - **옵서버(Observer)** : 관찰자. 상태 변경 알림을 받는 쪽. Subscriber, Consumer, EventListener, Listener 등으로 지칭하기도 한다.
    - 무엇으로 부르던, 발행-구독 형태의 관계를 맺고 있다면 옵서버 패턴에 속한다.

### 2. 예시 코드

**시나리오** : 자산관리시스템 개발 중이고, 사용자가 새로 가입할 경우 체험에 사용할 수 있는 프로모션 캐시를 준다.

#### 1. 예시 코드 : 옵서버 패턴 적용 전

```java
public class UserController {
    private UserService userService; // 의존성 주입
    private PromotionService promotionService; // 의존성 주입

    public Long register(String telephone, String password){
        …
        long userId = userService.register(telephone, password); // 회원 생성

        promotionService.issueNewUserExperienceCash(userId); // 회원 생성에 따른 프로모션 캐시 생성
        return userId;
    }
}
```

- 현재 사용자 등록 인터페이스가 사용자 등록 및 프로모션 캐시 발행이라는 서로 독립된 도메인의 비즈니스 로직 두 가지를 모두 수행.
    - SRP 원칙 위배.
    - 그럼에도 설계 원칙이 그러하듯, 확장되거나 수정할 일이 없다면 예시 코드와 같이 구현해도 문제 없음
- 하지만 요구사항이 빈번하게 변경될 가능성이 있는 경우 이러한 설계 및 구현이 수정될 필요가 있다.
    - 그렇게 된다면 OCP 원칙 위배가 된다.
        - 회원 대상 프로모션은 임시성 정책의 성격이 강하므로, 변경 및 확장의 가능성이 높다고 보는 것은 타당하다.
        - 예를 들어, 할인 쿠폰을 준다면 `register()` 메서드 내에선 `promotionService.issueNewDiscountCoupon(userId)` 와 같이 변경되어야 한다.

#### 2. 예시 코드 : 옵서버 패턴 적용 후(Synchronous & Blocking)

```java
public interface RegObserver {
    void handleRegSuccess(long userId);
}

// Observer 1
public class RegPromotionObserver implements RegObserver {
    private PromotionService promotionService; // 의존성 주입
    
    @Override
    public void handleRegSuccess(long userId){
        promotionService.issueNewUserExperienceCash(userId);
    }
}

// Observer 2
public class RegNotificationObserver implements RegObserver {
    private NotificationService notificationService;
    
    @Override
    public void handleRegSuccess(long userId){
        notificationService.sendInboxMessage(userId, "Welcome…");
    }
}

…

public class UserController {
    private UserService userService;
    private List<RegObserver> regObservers = new ArrayList<>();
    
    public void setRegObservers(List<RegObserver> observers){
        regObservers.addAll(observers);
    }

    public Long register(String telephone, String password){
        ..
        long userId = userService.register(telephone, password); // 회원 생성
        
        for(RegObserver observer : regObservers){
            observer.handleRegSuccess(userId);
        }
        // 옵서버들 모두 각자의 이벤트 처리 로직을 처리하고 완료한 뒤에야, 비로소 회원가입 로직이 종료된다.
        // 그렇기 때문에 Synchronous & Blocking.
        return userId;
    }
}
```

- 옵서버 패턴을 적용하여 당초 문제가 되던 OCP 위반부는 해결했으나, 이번엔 다른 부분에서 위반 발생.
    - 프로모션 캐시 -> 쿠폰으로 정책 변경 시, 이번엔 `RegPromotionObserver` 클래스의 `handleRegSuccess()` 코드를 수정해야함.
- 해당 위반은 옵서버블로부터 발행된 이벤트를 옵서버에게 전하는 로직의 책임을 담당하는 독자적인 컴포넌트를 도입하여 해결 가능하다.
    - Spring Framework 또한 AOP의 관점에서 이를 횡단 관심사로 정의하여 별도의 인터페이스를 통해 해당 기능 제공. (`ApplicationEventPublisher`, `EventListener`)

#### 3. 예시 코드 : 옵서버 패턴 적용 후(Asynchronous & Non-blocking)

```java
// Observer 1
public class RegPromotionObserver implements RegObserver {
    private PromotionService promotionService; // 의존성 주입
    
    @Override
    public void handleRegSuccess(Long userId){
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                promotionService.issueNewUserExperienceCash(userId);
            }   
        });
        thread.start();
    }
}

public class UserController {
    private UserService userService;
    private List<RegObserver> regObservers = new ArrayList<>();
    private Executor executor;
    
    public UserController(Executor executor) {
        this.executor = executor;
    }
    
    public void setRegObservers(List<RegObserver> observers){
        regObservers.addAll(observers);
    }

    public Long register(String telephone, String password){
        ..
        long userId = userService.register(telephone, password); // 회원 생성
        
        // 별도의 스레드를 생성하고 할당하여 각 스레드가 이벤트에 따른 비즈니스 로직 실행 및 처리.
        // 각 스레드의 종료 여부와 무관하게 return userId; 로 결과를 반환하며 현 userController 의 해당 로직 처리한 스레드는 스레드 풀로 반환.
        for(RegObserver observer : regObservers){
            executor.execute(new Runnable() {
                @Override
                public void run() {
                    observer.handleRegSuccess(userId);
                }
            });
        }
        return userId;
    }
}
```

## II. 템플릿 메서드 패턴(Template Method Pattern)

### 1. 정의

"템플릿 메서드 패턴은 하나의 메서드 안에 정의된 알고리즘 골격으로, 일부 작업 단계의 실행 책임을 하위 클래스로 위임 가능하도록 하여 하위 클래스가 알고리즘 전체 구조를 변경하지 않고 알고리즘의 일부 단계를 재정의할 수 있다." - <GoF의 디자인 패턴>

- 재사용과 확장 문제를 해결하기 위해 주로 활용되는 패턴

### 2. 예시 코드(Skeleton Code)

```java
public abstract class AbstractClass {

    /* Template Method */
    public final void templateMethod() {
        …
        method1();
        …
        method2();
    }
    
    // abstract 키워드를 활용해 구현 강제.
    protected abstract void method1();
    protected abstract void method2();
}

public class ConcreteClass1 extends AbstractClass {
    @Override
    protected void method1(){}

    @Override
    protected void method2(){}
}
```

### 3. 재사용성을 위한 활용 예시 : `InputStream`

```java
public abstract class InputStream implements Closeable {
    …
    
    public int read(byte b[], int off, int len) throws IOException {
    
        // 1. Validation Logic
        …
        
        // 2. Primitive Operation
        int c = read();
        
        // 3. Other logic
        … 
        return i;
    }

    public abstract int read() throws IOException;
}

// InputStream 을 구현한 구현체 예시 : ByteArrayInputStream
public class ByteArrayInputStream extends InputStream {
    …
    @Override
    public synchronized int read(){
       …
    }
}
```

> **Q. 왜 InputStream 은 템플릿 메서드 패턴을 활용해야만 했을까?**
> - InputStream 코드에서 abstract 키워드를 활용하여 명세된 `read()` 는 원시 연산(Primitive Operation).
> - 해당 원시 연산의 역할은 '데이터 원천으로부터 바이트를 읽어오는 것'.
> - 하지만 데이터 원천에 따라 바이트를 읽어오는 실제 구현 방식은 모두 다름.
> - InputStream 의 read(…args…) 메서드 내의 원시 연산 호출부 제외한 나머지 부분은 입력을 읽어오는 과정에서 반드시 공통적으로 수행되어야 하는 로직
> - 위의 상황을 고려했을 때, '전체 알고리즘은 유지하되 데이터 원천에 따라 읽어오는 방식만 다르게 구현하는 여러 클래스가 필요하다.' 라는 필요로 요약 가능하고 이러한 필요는 템플릿 메서드 패턴이 해결 가능.

### 4. 생각해보기

다음과 같은 코드에선 사용하지 않는 추상 메서드라도 반드시 구현해야 하는 문제가 있음.

```java
public abstract class AbstractClass {
    public final void templateMethod1() {
        …
        method1();
        …
        method2();
    }
    
    public final void templateMethod2() {
        …
        method3();
        method4();
    }

    protected abstract void method1();
    protected abstract void method2();
    protected abstract void method3();
    protected abstract void method4();
}
```

이 문제를 어떻게 해결할 수 있을 것인가?

**정답** : abstract 키워드를 활용해 컴파일 단계에서 구현을 강제하는 것이 아니라, 런타임 차원에서 구현을 강제할 수 있다.

```java
public abstract class AbstractClass {
    public final void templateMethod1() {
        ..
        method1();
        method2();
    }
    …
    …
    
    public void method1(){
        throw new MustOverrideThisMethodException();
    }
}
```
