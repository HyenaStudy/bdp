# SOLID

## 단일 책임 원칙(SRP: Single Responsibility Principle)
*클래스와 모듈은 하나의 책임만 지녀야 한다.*

### · 명확한 기준이 없다
클래스 설계 과정에서 보통 어떤 도메인을 기준으로 기능이 과다해지거나 다른 도메인과 주제가 섞일 우려가 있을 때 SRP를 위배했다고 판단할 수 있으나, 프로젝트 규모에 따라 다르다. 분리가 적을수록 기능이 집중돼서 SRP가 위배될 수 있으나, 분리가 과할수록 응집도가 낮아져서 유지보수성이 낮아진다.
### · 생각해보기
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
