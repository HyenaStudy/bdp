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


