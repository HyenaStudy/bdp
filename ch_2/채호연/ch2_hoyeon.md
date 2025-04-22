# 디자인 패턴의 아름다움 : Ch.2 - 객체지향 프로그래밍 패러다임

## 2.1 객체지향 프로그래밍 패러다임이란?

### 2.1.1 객체지향 프로그래밍 패러다임의 정의
객체지향 프로그래밍 패러다임을 한 문장으로 정의하는 것엔 한계가 있다.
<br>
이는 말 그대로 '패러다임', 즉 어떤 현상 혹은 문제, 세계를 바라보는 사고방식 그 자체이기 때문.
<br>
우리가 염세적 사고방식을 한 마디로 정의하기 어려운 것과 마찬가지이다.
<br>
본 책에선 그렇기 때문에 '객체지향 프로그래밍 패러다임의 특성'에 주목하여 이를 포착하고자 한다!

#### 객체지향 프로그래밍 패러다임의 핵심 특성
- _클래스_와 _객체_ 를 코드 구성의 기본 단위로 사용하는 방식  
- _**추상화**_ , _**캡슐화**_, _**상속**_, _**다형성**_ 의 활용을 코드 설계와 구현의 초석으로 삼는 접근  

### 2.1.2 객체지향 프로그래밍 패러다임의 역사
- 시뮬라(Simula)와 스몰토크(Smalltalk) 언어의 등장에 따른 이론적 기반 확립  
- 객체를 중심으로 한 프로그래밍 모델의 대두 과정  
- 소프트웨어 개발 관점에서 패러다임 전환이 가져온 영향  

### 2.1.3 객체지향 프로그래밍 패러다임을 구성하는 대표적인 네 가지 특성

#### 1. 추상화
- 불필요한 세부 사항을 감추고 핵심만 드러내는 설계 방식  
- 추상 클래스나 인터페이스를 사용하는 예제 코드로 확인 가능  
- 복잡성을 줄이고 설계 유연성을 높이는 목적 달성  
- 실제 설계 문제에서 공통된 행위나 속성을 추출하여 재사용성을 높이는 방식  

#### 2. 캡슐화
```java
public class Wallet {
  private String id;
  private long createTime;
  private BigDecimal balance;
  private long balanceLastModifiedTime;

  public Wallet(){
    this.id = IdGenerator.getInstance().generator();
    ...
  }

  public void increaseBalance(BigDecimal amount){
  ...
  }

}
```

- 데이터와 메서드를 묶어 정보 은닉을 실현하는 개념
- 접근 제어자를 통해 내부 구현을 보호하는 예제 코드로 확인 가능  
- 모듈성과 유지보수성 향상에 기여  
- 설계 문제에서 의존성과 충돌을 줄이고 보안성을 확보하는 목표 달성  

#### 3. 다형성
- 동일한 인터페이스로 다양한 구현을 수행하는 개념  
- 이
- 코드 재사용성과 확장성 증대  
- 설계 문제에서 여러 유형의 객체가 일관된 방식으로 동작하도록 통합하는 해결책

#### 4. 상속
- 부모 클래스의 속성과 메서드를 자식 클래스가 물려받는 구조  
- 코드 중복 제거와 계층적 설계 가능성에 대한 예제 코드로 확인 가능  
- 확장성과 유지보수성 향상에 대한 기여  
- 실제 설계 문제에서 계층 구조를 활용해 효율적인 기능 확장을 달성  

### 2.1.4 객체지향 프로그래밍 패러다임은 왜 사랑받는가?
- 인간의 사고방식과 유사한 구조로 코드 작성이 가능하다는 점 & 인간적 사고방식으로 현실 세계를 모델링하기에 가장 손쉬운 관점
- 재사용성과 유지보수성 향상에 따른 개발 효율성 증대
- 팀 협업 과정에서의 역할 분담과 책임 분리에 대한 용이성  
- 복잡한 문제를 단순화하고 확장 가능한 아키텍처를 제시하는 강점  

---

## 2.2 객체지향과 절차지향, 함수형 프로그래밍 패러다임의 비교

### 2.2.1 절차지향
- 순차적 로직 중심의 프로그래밍 방식(ex. 여러 도메인 서비스 로직을 순차적으로 실행하도록 작성한 Facade 클래스 메서드)
- 상태를 공유하며 순서대로 작업을 처리하는 예제 코드로 확인 가능  
- 객체지향과의 차이: 데이터와 기능의 분리, 모듈 단위보다는 함수 또는 프로시저 중심 구성  
- 장점과 단점:  
  - **장점**: 간단한 구조, 직관적인 흐름 파악 용이  
  - **단점**: 대규모 프로젝트에서 유지보수 어려움, 재사용성 저하

### 2.2.2 함수형 프로그래밍 패러다임
- 순수 함수와 불변 데이터를 중심으로 하는 패러다임  
- 고차 함수와 람다 함수를 적극적으로 활용하는 예제 코드로 확인 가능  
- 객체지향과의 차이: 상태 변경 최소화, 부수 효과 억제, 선언적 프로그래밍 방식 지향  
- 장점과 단점:  
  - **장점**: 사이드 이펙트 감소(순수한 함수형 프로그래밍 패러다임의 경우 철저히 함수와 그 멱등성에 근거하기 때문. 즉, 함수의 인자와 함수의 동작방식에 의해서만 결과가 결정), 병렬 처리와 테스트 편의성 증대
  - **단점**: 복잡한 객체 모델 표현의 어려움, 과도한 추상화로 인한 학습 곤란
 
### 2.2.3 같은 요구사항 : 객체지향 vs 절차지향 vs 함수형 프로그래밍
```
요구사항 : "1부터 N까지의 숫자 중, 3의 배수를 제외한 모든 숫자의 합을 구한다."

```

#### 1. 객체지향
```java
public class NumberSummer {
    private int n;

    public NumberSummer(int n) {
        this.n = n;
    }

    public int sumExcludingMultiplesOfThree() {
        int total = 0;
        for (int i = 1; i <= n; i++) {
            if (i % 3 != 0) {
                total += i;
            }
        }
        return total;
    }

    public static void main(String[] args) {
        NumberSummer summer = new NumberSummer(10);
        int result = summer.sumExcludingMultiplesOfThree();
        System.out.println("객체지향 방식: " + result); 
    }
}
```

#### 2. 절차지향
```
public class ProceduralExample {
    public static int sumExcludingMultiplesOfThreeProcedural(int n) {
        int total = 0;
        for (int i = 1; i <= n; i++) {
            if (i % 3 != 0) {
                total += i;
            }
        }
        return total;
    }

    public static void main(String[] args) {
        int result = sumExcludingMultiplesOfThreeProcedural(10);
        System.out.println("절차지향 방식: " + result);
    }
}
```

#### 3. 함수형
```java
import java.util.stream.IntStream;

public class FunctionalExample {
    public static int sumExcludingMultiplesOfThreeFunctional(int n) {
        // IntStream.rangeClosed(1, n)으로 1부터 n까지의 범위를 생성
        // filter(...)로 3의 배수가 아닌 값만 필터링
        // sum()을 통해 합계 계산
        return IntStream.rangeClosed(1, n)
                        .filter(i -> i % 3 != 0)
                        .sum();
    }

    public static void main(String[] args) {
        int result = sumExcludingMultiplesOfThreeFunctional(10);
        System.out.println("함수형 방식: " + result);
    }
}
```

여러분들은 어떤 접근이 가장 마음이 편안한가요?
