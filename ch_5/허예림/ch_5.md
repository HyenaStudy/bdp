## Chater 5 리팩터링 기법

### 5.1 리팩터링의 네가지 요소 목적, 대상, 시기, 방법

**리팩토링의 목적**

- 리팩터링은 코드에 대한 이해를 쉽게 하기 위해 소프트웨어 내부 구조를 개선하는 것으로 소프트웨어의 **외부 동작을 변경하지 않고** 수정 비용을 줄이는 것을 목적으로 한다.
- 리팩터링은 코드 품질을 보장하는 효과적인 수단으로 코드 품질 저하를 효과적으로 방지할 수 있다.
- 고품질 코드는 훌륭한 설계 한 번에 나오는 것이 아니라 반복적인 작업의 결과로 나오는 것이다.
- 일시적으로 불완전한 설계를 덮을 수 있는 과도한 설계를 피하는 효과적인 방법이다.

**리팩터링의 대상**

- 대규모 리팩터링 : 시스템, 모듈, 코드 구조, 클래스 간 관계의 리팩터링을 포함하여 최상위 코드 설계를 리팩터링 하는 것을 말한다. 대규모 리팩터링에는 계층화, 모듈화, 분리, 재사용 가능한 구성 요소 추상화가 사용된다.
- 소규모 리팩터링 : 표준 명명, 표준 주석, 초대형 클래스와 함수 제거, 중복 코드 추출과 같이 주로 클래스, 함수, 변수 수준에서 코드 세부 정보를 리팩터링 하는 것을 말한다. 소규모 리팩토링은 수정이 집중적으로 이루어지며, 프로세스가 간단하고 조작성이 강하며, 소요 시간이 짧고, 버그가 발생할 위험이 낮다.

**리팩터링의 시기**

- 코드가 이미 망가진  후에는 오히려 늦었다.
- 지속 가능하고 진화적인 리팩터링 계획을 탐구해야 한다. → 지속적인 리팩터링

**리팩터링의 방법**

- 대규모 리팩터링을 수행할 때는 사전에 종합적인 리팩터링 계획을 수립해, 질서있고 단계적으로 진행해야한다.
- 각 단계에서 코드의 일부를 리팩터링하여 제출, 테스트, 실행하는 데 문제가 없으면 다음 단계의 리팩터링을 수행하여 코드 저장소의 코드가 항상 실행 가능한 상태가 되도록 해야한다.
- 대규모 리팩터링의 각 단계에서 리팩터링의 영향을 받는 코드의 범위를 제어하고 기존 코드와의 호환성을 고려하고 필요할 때 호환성을 달성하기 위해 변경 코드를 제공해야 한다.
- 소규모 리팩터링은 언제든 시간이 있을 때 할 수 있으며, Checkstyle, FindBugs, PMD등을 사용하여 코드에서 문제를 자동으로 찾은 다음 해당 부분만 리팩터링 할 수 있다.
- 깨진 유리창 효과유의

### 5.2 단위 테스트

**단위 테스트에 대해**

- 단위 테스트는 코드의 정확성을 검증하기 위한 테스트로, 테스트 엔지니어가 아닌 개발자가 직접 작성한다.
- 통합 테스트보다 범위가 작으며, 전체 시스템이 아닌 특정 클래스 또는 메서드를 독립적으로 테스트한다.
- 테스트 대상은 클래스 또는 함수로 제한되며, 예상되는 입력값과 그에 따른 반환값을 검증하는 방식이다.

**단위 테스트 코드를 작성하는 이유**

- 단위 테스트는 프로그래머가 코드에서 버그를 찾는데 도움이 될 수 있다.
    - 매번 코드를 제출할 때마다 완벽한 단위 테스트를 설계하는 것은 중요하며, 이런 코드는 거의 버그가 없다.
- 단위 테스트는 프로그래머가 코드 설계에서 문제를 찾는 데 도움이 될 수 있다.
    - 코드에 대한 단위 테스트를 설계하는 데 어려움을 겪고 있고, 단위 테스트 프레임워크의 고급 기능에 의존해야 하는 경우라면 의존성 주입을 사용하지 않거나 전역변수와 정적함수를 많이 사용하고 결합성이 높은 코드처럼 설계가 비합리적임을 의미하는 경우가 많다.
- 단위 테스트는 통합 테스트를 보완하는 강력한 도구다.
    - 모든 예외를 재현하는 것이 어렵기 때문에 테스트 환경의 부족한 부분을 단위 테스트에서 보완할 수 있다.
    - 목 메서드로 반환 값을 제어하고 비정상적인 상황을 재현하여 해당 상황에서의 코드 실행 결과를 테스트 할 수 있다.
    - 모든 클래스와 함수가 예상대로 실행된다는 것을 보장할 수 있다면 전체 시스템에 문제가 발생할 가능성이 줄어든다.
- 단위 테스트 코드를 작성하는 과정은 코드 리팩터링 과정에 해당한다.
    - 코드를 작성할 때 모든 상황을 명확하게 고려하는 것은 어렵지만 단위 테스트 코드를 작성하는 것을 통해 코드를 다시 한번 검토할 수 있고 테스트 용이성이 낮은 것과 같은 코드 설계상의 문제를 찾아 낼 수 있다.
- 단위 테스트는 프로그래머가 코드에 빠르게 익숙해지도록 도와준다.
    - 상황별 이해하기 어려운 코드가 존재할 때 단위 테스트가 문서와 주석의 역할을 대신할 수 있다.
    - 단위테스트를 사용하여 코드가 구현하는 내용, 고려해야 할 특별한 경우, 처리해야 하는 경계 조건을 알기위해 코드를 깊이 읽을 필요가 없게 된다.
- 단위 테스트는 테스트 주도 개발을 개선하고 대처할 수 있다.
    - 테스트 주도 개발은 자주 언급되지만 거의 구현되지 않는 개발패턴으로 이 패턴의 핵심사상은 테스트 케이스가 코드보다 먼저 작성되는 것이다
    - 코드를 먼저 작성하고 이에 맞춰 단위 테스트를 설계하고 리팩터링하는 것으로 이를 대체할 수 있다.

**단위 테스트를 설계하는 방법**

- 단위 테스트를 설계하는 것은 시간이 많이 걸리는 일인가?
    - 단위 테스트를 위한 코드 양이 많고 때로는 테스트 하려는 코드보다 많아지지만 설계를 고려할 필요가 없기 떄문에 시간상으로는 오래걸리지 않는다.
- 단위 테스트 코드의 품질에 대한 요구사항이 있는가?
    - 프로덕션 환경에서 진행되지 않고 독립적이고 의존적이지 않기 때문에 요구사항이 낮은편이다.
- 단위 테스트의 커버리지가 높으면 그것만으로 충분한가?
    - 단위 테스트 커버리지는 단위 테스트의 품질을 측정하는 데 자주 사용되는 값으로 쉽게 정량화할 수 있다.
    - Jacoco, cobertura, Emma, clover등 단위 테스트 커버리지를 측정하는 통계도구가 여러 가지 있으며, 간단한 구문 커버리지부터 더 복잡한 조건 커버리지, 결정 커버리지, 경로 커버리지처럼 커버리지를 게산하는 방법도 여러가지가 있다.
- 단위 테스트 코드를 작성할 때 코드의 구현 논리를 이해하는 것이 필요한가?
    - 높은 커버리지를 추구하기 위해 코드를 한줄씩 따라가면서 구현 논리자체에 대한  단위 테스트를 설계하면 안된다.
- 단위 테스트 프레임워크를 선택하는 방법은 무엇인가?
    - 회사나 팀에서 사용하는 것을 따라도 문제 없다.

**단위 테스트를 작성하기 어려운 이유**

- 시간 이슈
- 단위 테스트를 하나씩 완료하는 것이 불가능한 상황, 시간이 낭지이며 불필요하다고 여겨짐
- 작성이 완료된 코드를 커밋하면 이코드는 블랙박스 테스트 팀에 던져져 테스트가 이루어지고, 이떄 발견된 문제를 개발팀에 다시 피드백하여 수정됨
- 이때 발견되지 않은 문제는 서비스에서 문제가 발생해야 수정되는 사이클을 반복할 뿐

### 5.3 코드 테스트 용이성

**테스트 가능한 코드를 작성하는 방법**

- 네트워크 중단, 시간 초과, Redis 서비스와 Rpc서비스를 사용할 수 없는 환경은 단위 테스트 실행에 영향을 줄 수 있다.
- 코드가 외부 시스템, 데이터 베이스, 네트워크, 파일 시스템과 같이 제어할 수 없는 구성 요소에 종속되는 경우 이 종속 관계를 끊을 방법이 필요하며, 이를 모의 구현 또는 Mock이라고 한다.
- 모의 구현의 동작 방식
    - 모의 객체는 특정 메서드나 함수를 호출할 때, 미리 설정된 값이나 동작을 제공한다 이를 통해 시스템의 상태 변경 없이 테스트 중에 실제로 호출된 메서드가 예상대로 호출되었는 지 검증할 수 있다.
- 싱글턴 클래스는 전역 변수와 동일하기 때문에 메서드를 상속하거나 재정의하는 방식으로 임의값을 넣을 수도 없고, 의존성 주입으로 대체할 수 없다.
- 싱글턴 클래스를 리팩터링 할 수 없다면 잠금 논리를 캡슐화하는 방식으로 처리할 수 있다.

testcode first 규칙

- Fast : 테스트 코드는 실행 속도가 빨라 자주 실행하는 데 부담이 없어야 함
- Indenpendent : 테스트가 서로 독립되면 안됨, 어떤 순서로 실행해도 문제가 없어야함
- Repeatable : 어떤 환경에서도 반복가능해야함
- Self-validating : bool값으로 결과 도출 필수
- Timely : 단위 테스트는 실제 코드를 구현하기 직전에 구현한다.

**Fixture**

- Pytest와 같은 프레임워크에서 사용되는 개념, 실행 전후 테스트 작업 처리 하는 역할
- set - up : 테스트 수행 전 리소스 준비
    - 테스트가 실행될 때마다 동일한 초기 상테에서 테스트를 진행할 수 있도록 준비
- tear-down : 각각의 테스트 케이스가 끝난 후 실행되는 코드 및 리소스 정리
    - 테스트 케이스 간의 독립성을 유지하고 테스트 환경이나 리소스의 부정적인 영향을 다음 테스트에 전파시키지 않도록 한다.

```python
import pytest

@pytest.fixture
def sample_data():
    data = [1, 2, 3, 4, 5]
    print("Setting up sample data")
    yield data
    print("Cleaning up after test")

def test_sum(sample_data):
    result = sum(sample_data)
    assert result == 15

```

- 반복적인 작업을 자동화 하여 코드의 재사용성을 높이고, 각 테스트가 독립적이고 깨끗한 환경에서 실행되도록 보장
- Fixture는 다른 fixture를 **의존 하도록 설계 가능**. ex, DB 연결을 위한 fixture가 있고, 이를 사용하는 테스트에서 **DB 연결이 준비되었는지 확인**한 뒤 그에 의존하는 추가적인 자원을 설정
- JUnit - @BeforeEach, @AfterEach, Mock - @Mock, @InjectMocks 로 각각의 역할 수행
- spring을 사용하지 않을 시

```python
import static org.mockito.Mockito.*;
import org.junit.Before;
import org.junit.Test;

public class MyTest {

    private MyService myServiceMock; 
    private MyProcessor myProcessor; 

    @Before
    public void setUp() {
        myServiceMock = mock(MyService.class);

        myProcessor = new MyProcessor(myServiceMock);

        when(myServiceMock.getData()).thenReturn("Test Data");
    }

    @Test
    public void testProcessor() {
        String result = myProcessor.processData();
        
        assert(result.equals("Test Data"));
    }
}

```
