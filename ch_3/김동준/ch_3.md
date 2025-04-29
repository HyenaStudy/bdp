# SOLID

## 단일 책임 원칙(SRP: Single Responsibility Principle)
*클래스와 모듈은 하나의 책임만 지녀야 한다.*

### 1) 명확한 기준이 없다
클래스 설계 과정에서 보통 어떤 도메인을 기준으로 기능이 과다해지거나 다른 도메인과 주제가 섞일 우려가 있을 때 SRP를 위배했다고 판단할 수 있으나, 프로젝트 규모에 따라 다르다. 분리가 적을수록 기능이 집중돼서 SRP가 위배될 수 있으나, 분리가 과할수록 응집도가 낮아져서 유지보수성이 낮아진다.
### 2) 생각해보기: 클래스 설계 외의 SRP
크게는 **패키지** 역시 SRP의 대상이 될 수 있다. **DDD**, **3계층 패키지**가 대표적인 예시다.
```scss
src
 ├── domain // 핵심 비즈니스 규칙
 │    └── user
 │         ├── User.java // 사용자 Entity
 │         ├── UserRepository.java // 사용자 저장소 인터페이스
 │         ├── Password.java // 비밀번호 인코딩 객체
 │         └── UserStatus.java  // 사용자 상태 Enum 등
 │
 ├── application // 비즈니스 흐름, 유즈케이스(서비스)
 │    └── user
 │         ├── signup // 회원가입 서비스, DTO
 │         │     ├── SignUpUserService.java
 │         │     └── SignUpRequest.java
 │         │
 │         ├── login // 로그인 서비스, DTO
 │         │     ├── LoginUserService.java
 │         │     └── LoginRequest.java
 │         │
 │         └── password // 비밀번호 서비스, DTO
 │               ├── ChangePasswordService.java
 │               └── ChangePasswordRequest.java
 │
 ├── infrastructure // 설정, 외부 연동
 ├── presentation // 요청, 응답 처리
 └── common // 공통 유틸리티
```
작게는 **메소드** 또한 SRP의 대상이 될 수 있다.
```java
// 덧셈 메소드인데 다른 사칙연산 로직이 들어가면 결합도가 높아진다
public int plus(int... nums) {
    int sum = 0;

    for (int num : nums) {
        sum += num;
    }

    return sum;
}
```

## 개방 폐쇄 원칙(OCP: Open-Closed Principle)
*확장에는 열려있되, 수정에는 닫혀있어야 한다.*
### 1) 확장과 수정의 구별
교재에서는 새로운 기능 추가 시점에서 기존의 코드를 건드리는지 여부를 기준으로 제시한다. 즉, 기존 코드를 건드려야 하면 수정이고 기존 코드를 밑바탕 삼아서 새로운 코드가 추가되면 확장이라고 본다.

예전에 모던 자바 인 액션 교재에서 동작의 파라미터화를 통한 함수형 리팩토링 예제가 있었는데, 딱 그 과정이 OCP를 실현하는 예시 과정으로 봐도 좋을듯. 다만, 그 예시 또한 결국 코드가 수정됐으므로 OCP를 위배하는 것 아니냐고 볼 수 있는데, 개인적으로는 **코드 수정 당시의 시점이 아닌, 미래에 있을 리팩토링까지 고려해서 수정과 확장을 구별**해야 한다고 생각한다. 디자인 패턴 전반이 이 OCP를 실현하기 위한 수단이다.

### 2) 생각해보기: 왜 확장에는 개방, 수정에는 폐쇄일까

이 질문이 제일 간단했는데, 소프트웨어는 계속 변하고 확장되기 때문이다. 즉 한번 개발된 소프트웨어는 거기서 끝이 아니라 향후 지속적인 변화가 불가피하다. 이를 위해서 OCP를 준수하는 게 좋다.

## 리스코프 치환 원칙(LSP: Liskov Substitution Principle)
*하위 클래스 객체는 프로그램 내의 상위 클래스가 나타나는 모든 상황에서 대체 가능하다.*
### 1) 다형성과의 차이점
교재에서는 하위 구현체에서 예외 발생 로직 추가의 경우로 차이점을 설명하고 있다.
```java
public class A {
  public Object method() {
    // ...
    return object;
  }
}

public class B extends A {
  @Override
  public Object method() {
    // ...
    if (case) {
        return object;
    }
    return super.method();
  }
}

public class C extends A {
  @Override
  public Object method() {
    // ...
    if (!case) {
        throw new SomeException();
    }
    return object;
  }
}
```

A 클래스를 상속해서 조건 검증 로직을 추가한 B와 C 클래스는 그 기능적으로는 상당히 유사하다. 다만 A 클래스 타입이 쓰여진 상황에서 B 클래스로 치환했을 때는 아무런 논리적 동작의 변화가 없으나, C 클래스로 치환하면 예외가 발생할 수 있다. 즉, 전체 프로그램 논리적 동작이 변경됐다. B와 C 전부 다형성 구문을 통해 코드를 구현했으나, C는 LSP를 따르지 않는다는 차이점이 있다.

다만, 상위 클래스는 그냥 동작하는데, 하위 클래스에서 특정 분기별 예외가 발생한다고 LSP가 지켜지지 않는다고 해서 꼭 B 클래스처럼 코드를 짜는 게 좋은 것만은 아니다. LSP를 강하게 준수할 수록 다형성의 의미가 퇴색되는 거지만, SOLID 원칙은 그저 코드를 잘 짜기 위한 **수단**에 불과하고, 다형성 같은 객체지향 특성은 코드 작성의 본질적인 **핵심**이기 때문에 개인적으로는 다형성이 조금 더 우선하면서 과한 다형성을 막기 위한 수단으로 LSP가 존재한다고 생각한다.

```java
```java
public class A {
  // 상위 클래스에서 예외를 대비할 수 있도록 논리적 흐름을 통일
  // 이른바 어떤 상황에서 어떤 예외가 내뱉어질 수 있다는 '계약'을 걺
  public Object method() throws SomeException {
    // ...
    return object;
  }
}

public class C extends A {
  @Override
  public Object method() throws SomeException {
    // ...
    if (!case) {
        throw new SomeException();
    }
    return object;
  }
}
```
위와 같은 코드 설계를 **계약에 따른 설계**라고 할 수 있다.

### 2) 생각해보기: LSP의 중요성
위의 목차에서 답이 미리 나왔는데, 결국 다형성을 우선하지만 과도한 다형성으로 인한 상위 동작의 범위를 벗어나는 걸 막기 위한 수단이 바로 LSP라고 생각한다.

## 인터페이스 분리 원칙(ISP: Interface Segregation Principle)
*클라이언트에서 필요하지 않은 인터페이스를 사용하도록 강요해서는 안 된다.*
### 1) 인터페이스의 정의
인터페이스는 특정 API가 될 수도 있고, API들의 집합도 될 수도 있고, 그냥 자바 같은 프로그래밍 언어에서 제공하는 인터페이스가 될 수도 있다. 회원 관리를 주제로 한 인터페이스 내부에 등록, 로그인, 조회 등의 추상 기능들이 정의되어 있을 때, **삭제** 기능이 추가돼야 한다면 단순 회원 관리 인터페이스에 메소드를 추가할 수도 있지만 보안적인 이슈(사용자 삭제의 위험부담 고려)를 고려했을 때, 별개의 인터페이스(엄격한 회원 관리)를 구축하고 그 내부에 삭제 기능을 신규 구현하는 것이 조금 더 안전하다.

이 과정에서 삭제 기능은 별개의 인터페이스를 통해 제공되므로 기존의 사용자 인터페이스를 활용하던 기능에서의 삭제 추가가 아닌, 단순한 인터페이스의 기능 추가라면 해당 클라이언트에게 삭제 메소드의 사용을 강요하지 않을 수 있다. 이 과정이 ISP 준수의 예시라고 할 수 있다. 또한, 이 내용은 포괄적인 기능들이 집중된 인터페이스를 분리하면서 핫 업데이트 등에 대비하는 경우도 포함된다.

### 2) 생각해보기: AtomicInteger 클래스의 getAndIncrement() 메소드가 ISP를 준수하는가?

`getAndIncrement()` 메소드는 원자성을 확보한 값을 조회하고, 해당 값을 1 증가시킨 다음, 저장하게 된다. 즉 **조회, 연산, 저장**이라는 3개의 기능이 하나의 메소드에 묶여있는 셈이다. 표면적으로 봤을 때는 ISP, 더불어 SRP까지 위배되고 있다고 볼 수 있지만 기본 제공되는 패키지의 클래스 및 메소드여서 조금 더 세부적으로 파고들기로 했다.

해당 메소드의 구조는 다음과 같다.

```java
public class AtomicInteger extends Number implements java.io.Serializable {

    // ...
    private static final Unsafe U = Unsafe.getUnsafe();
    private static final long VALUE
        = U.objectFieldOffset(AtomicInteger.class, "value");

    // ...
    public final int getAndIncrement() {
        return U.getAndAddInt(this, VALUE, 1);
    }
```
`AtomicInteger` 클래스의 필드에는 저수준 메모리 조작이 가능한 `Unsafe` 불변 클래스를 지녔다. `getAndIncrement()` 메소드는 `Unsafe` 클래스의 메소드를 통해 기능을 구현한다.
```java
public final class Unsafe {

    // ...
    @IntrinsicCandidate
    public final int getAndAddInt(Object o, long offset, int delta) {
        int v;
        do {
            v = getIntVolatile(o, offset);
        } while (!weakCompareAndSetInt(o, offset, v, v + delta));
        return v;
    }
```
`Unsafe` 내부의 메소드에서는 **다른 스레드가 바꾼 값을 즉시 보는 '가시성'을 보장하도록** JVM이 캐시가 아닌 메인 메모리에서 바로 읽은 값을 반환하되, CAS(Compare And Swap) 알고리즘을 통해 값을 원자적으로 반환하게 된다. 조금 더 구체적인 과정은

>- `v = getIntVolatile(o, offset);` : 메인 메모리에서 바로 값을 읽어옴
>- `!weakCompareAndSetInt(o, offset, v, v + delta)` : 지금 읽은 `v`가 동일하다면 `v + delta`로 교체 시도, 그 과정에서 다른 스레드가 먼저 값을 바꿨다면 CAS 실패 -> 루프 반복

여기서 `getAndIncrement()` 메소드로 한정한다면 `delta`는 1이 된다. 결국 정리하자면,

1. 클라이언트가 단순히 조회의 목적으로 쓴 거면, 그건 원칙 위반이 아닌 클라이언트의 오용이다.
2. 클라이언트가 목적에 맞게 썼다면, 결국 증가를 위해서는 읽기가 필요하고(스레드 안전 특성상) 두 기능이 불가분적으로 묶여야 하기 때문에 SRP 위반이 아니다.
3. 클라이언트가 목적에 맞게 썼다면, 해당 의도가 담겨져 있으며 단순히 조회만을 원했다면 조회 메소드(get()) 또한 이미 존재하기 때문에 필요에 맞게 쓰면 돼서 클라이언트에게 사용을 강제하지 않으므로 ISP 위반도 아니다.

라고 결론지을 수 있다.

## 의존 역전 원칙(DIP: Dependency Inversion Principle)
*상위 모듈이 하위 모듈에 의존하지 않고 추상화에 의존해야 한다.*
### 1) 스프링부트에서의 DIP 실현
단순히 IoC 컨테이너에 빈이 등록되면서 상호간 의존성 주입(DI)이 이뤄지는 내용으로 알고 있지만 조금 더 깊게만 파보면 **Tomcat 내장 엔진**이 웹 앱 실행을 위한 컨테이너면서 동시에 해당 앱 코드를 배포하면 별도 작업 없이 톰캣 컨테이너에서 호출 + 실행이 가능하다. 즉, 톰캣이 상위 모듈이고 웹 앱 코드는 하위 모듈에 해당한다. 둘 사이에 직접적인 의존성이 존재하진 않으나 둘 다 동일한 추상체(계약)인 서블릿 사양에 의존하게 된다. 서블릿의 실제 구현체 중 하나가 바로 **DispatcherServlet**이다.

### 2) 생각해보기: 구현이 아닌 인터페이스 기반, 의존성 주입의 차이점

인터페이스 기반은 구현이 아닌 추상에 의존함으로써 코드의 교체가 쉽고 유지보수가 용이해진다. 의존성 주입은 객체의 생성 책임을 외부에서 지는 것으로 결합도를 낮추는 것이다.
