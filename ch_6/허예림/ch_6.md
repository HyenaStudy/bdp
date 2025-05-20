## Chapter6. 생성 디자인 패턴

### 6.1 싱글턴 패턴

**정의**

- 어떤 클래스의 객체 또는 인스턴스를 단 하나만 생성하고 이를 전역에서 공유하도록 하는 생성 패턴

**구현**

https://readystory.tistory.com/116

<aside>
➡️

- 생성자는 new 예약어를 통한 인스턴스 생성을 피하기 위해 private 접근 권한을 가지고 있어야 한다.
- 객체가 생성될 때 스레드 안전성을 보장하는 지 확인해야한다.
- 지연 로딩을 지원하는 지 여부를 확인해야 한다.
- getInstance() 함수의 성능이 충분해야 한다.
</aside>

- 즉시 초기화 (Eager Initialization)
    - 클래스가 로드되는 시점에 즉시 싱글턴 인스턴스를 생성하는 방식
    - Java 에서 클래스 필드에 static final 로 인스턴스 생성하는 경우
    - Python에서 모듈 로드 시 객체를 생성해두거나 클래스 정의 이후 바로 객체를 하나 만들어 두는 방식
    - 싱글턴은 딱 한번만 초기화 되어야 싱글턴이다, 만약 생성자에 매개변수를 매달아 초기화한다면 싱글턴이 아닌것이다.
    - 장점
        - 쓰레드 안전성 : 클래스 로딩 때 한번만 생성되므로 따로 등기화가 필요 없으며, 멀티 쓰레드 환경에서 안전
        - 즉시 사용 가능 : 애플리케이션 시작과 동시에 인스턴스가 준비되므로, getInstance() 호출 시 지연없이 객체 획득 가능
    - 단점
        - 자원 낭비 → 해당 객체를 사용하지 않더라도 클래스 로딩 시 무조건 인스턴스를 생성하므로 메모리 낭비나 초기 부하가 있을 수 있다
        - 유연성 부족 : 나중에 인스턴스 시점을 제어할 수 없고 생성자에 매개변수를 줄 수 없다.
    - https://stackoverflow.com/questions/1050991/singleton-with-arguments-in-java
- 늦은 초기화 (Lazy Initialization)
    - 필요한 순간까지 인스턴스 생성을 미루는 방식
    - 장점
        - 지연된 자원 사용으로 객체 생성 비용이 높지 않음
    - 단점
        - 멀티 쓰레드 안전 문제 (동시성 제어가 없음) → synchrinized로 개선 가능
        
        ```python
        public class LazySingleton {
            private static LazySingleton instance; 
            private LazySingleton() { }
            public static LazySingleton getInstance() {
                if (instance == null) {                  
                    instance = new LazySingleton();     
                }
                return instance;
            }
        }
        ```
        
        ```python
        class LazySingleton:
            _instance = None
            def __new__(cls, *args, **kwargs):
                if cls._instance is None:            
                    cls._instance = super().__new__(cls)
                return cls._instance
        
        # 사용 예
        a = LazySingleton()
        b = LazySingleton()
        print(a is b)  # True, 두 변수는 같은 인스턴스를 가리킴
        ```
        
- 이중 잠금 (Double-Checked Locking)
    - Lazy initialization의 변형으로 두번의 체크로 동기화 범위를 최소화하여 성능을 개선한 방식
    - 먼저 인스턴스 존재 여부를 체크하고 없을 때만 동기화 블록에 들어가 다시 한 번 체크 후 생성하는 패턴
    - Java의 volatile 인스턴스 변수를 CPU에 캐시가 아닌 메인 메모리에서 읽도록 하여 초기화 순서와 가시성 문제를 해결
    - 장점
        - 인스턴스가 한번 생성되기 떄문에 동기화로 인한 성능저하가 거의 없음, 초기화 비용과 동기화 오버헤드는 최초 1회이고 그 이후는 거의 Eager와 동일
        - 쓰레드 안전성
    - 단점
        - 비교적 복잡한 구현
        - 가독성 저하
    - Python에서도 이중 잠금을 비슷하게 구현할 수 있지만, Python 인터프리터의  GIL 특성상 순수 Pytrhon 코드에서는 위와 같은 경쟁상태가 잘 방생하지 않는 편입니다. 다만 멀티쓰레딩 상황에서는 정확한 동작을 보장하려면 Python도 threading.Lock등을 이용해 임계구역을 보호
    
    ```python
    public class DoubleCheckedSingleton {
        private static volatile DoubleCheckedSingleton instance;
        private DoubleCheckedSingleton() { }
        public static DoubleCheckedSingleton getInstance() {
            if (instance == null) {                        
                synchronized (DoubleCheckedSingleton.class) {
                    if (instance == null) {   
                        instance = new DoubleCheckedSingleton();
                    }
                }
            }
            return instance;
        }
    }
    
    ```
    
- 홀더에 의한 초기화 (Initialization-on-demand Holder Idiom == Bill Pugh Singleton)
    - 자바에서 주로 사용하는 기법
    - 클래스 안에 싱글턴 인스턴스를 보관하는 정적 내부 클래스를 두는 방법
    - 외부 클래스의 초기화 시점에는 로드되지 않다가, getInstance()호출 시 처음 참조될때 비로소 로드되며 그 안에서 인스턴스를 생성
    - 명시적으로 동기화를 진행하지 않아도 Java 클래스 로더 매커니즘에 의해 한번만 초기화가 보장되므로 lazy 초기화와 쓰레드 안전성을 확보 가능
    - 장점 : Lazy Initialization이면서 동기화 없이 멀티 쓰레드에 안전, 높은 성능 (동기화가 필요 없고 런타임 오버헤드가 전혀 없다고 봐도 무방)
    - 단점 : 다른 방식에 비해 널리 알려진 방식이 아니라 코드의 의도를 이해하기 어려울 수 있음
    
    ```python
    public class HolderSingleton {
        private HolderSingleton() {}

        private static class SingletonHolder {
            private static final HolderSingleton INSTANCE = new HolderSingleton();
        }
        public static HolderSingleton getInstance() {
            return SingletonHolder.INSTANCE;
        }
    }
    pedrolopesdev.com
    . JVM의 클래스 로딩 과정은 본질적으로 쓰레드 세이프이므로, 별도의 락 없이도 동시성 문제 없이 유일한 인스턴스가 생성 및 반환됩니다
    
    ```
    
- 열거 (Enum)
    - Java에서 enum 타입을 활용한 단일 요소를 가진 열거체로 싱글턴을 구현한 방식
    - 열겨형 클래스는 로딩 시 단 한번만 인스턴스 화되며 직렬화 처리도 자동으로 제공됩니다.
    - 장점
        - 구현 용이함, 명시적 싱글턴 보장, 직렬화 안전성 (Java가 유일함 을 보장함)
    - 단점 :
        - 지연 로딩 불가 : ENUM 클래스 특성상 클래스 로딩 시 인스턴스가 생성되므로 Eager Intialization 방식을 사용
        - 상속 및 확장 불가 : enum은 다른 클래스를 상속할 수 없고 필요에 따라 인터페이스 구현은 가능하지만 클래스 확장이 블가능
        - **Java 전용:** 이 방식은 Java에 특화된 기법이므로, Python 등 다른 언어에서는 적용할 수 없습니다

**싱글턴 패턴의 대안**

- 의존성 주입 : 어떤 객체가 필요한 의존 객체를 직접 찾지 않고 외부에서 주입 받는 설계 방식
    - 싱글턴의 장점인 한가지 인스턴스 유지를 살리면서 글로벌한 하드코딩 의존성을 제거하는 접근 가능
- 서비스 로케이터 패턴 : 필요한 객체들을 미리 등록해둔 저장소에서 요청한 서비스를 반환해주는 패턴
    - 싱글턴과 비슷하지만 인터페이스를 기반으로 구현을 숨길 수 있고 테스트 시 교체가 비교적 쉽다
    [https://velog.io/@tco0427/DIDependency-Injection와-서비스-로케이터#서비스-로케이터를-이용한-의존-객체-사용](https://velog.io/@tco0427/DIDependency-Injection%EC%99%80-%EC%84%9C%EB%B9%84%EC%8A%A4-%EB%A1%9C%EC%BC%80%EC%9D%B4%ED%84%B0#%EC%84%9C%EB%B9%84%EC%8A%A4-%EB%A1%9C%EC%BC%80%EC%9D%B4%ED%84%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%98%EC%A1%B4-%EA%B0%9D%EC%B2%B4-%EC%82%AC%EC%9A%A9)

### 6.2 싱글턴 패턴(2)

**싱글턴 패턴의 유일성**

- 싱글턴 패턴은 프로세스 내에서 단 하나의 인스턴스만 존재하도록 보장하는 것이 핵심 개념
- 직렬화 : 객체를 직렬화한 후 역질렬화 하면 유일성이 꺠지게 됨 → readResolve()로 해소
- refelction :  자바의 reflection api를 통해 private 생성자 호출 시 새로운 인스턴스 생성 가능 따라서 생성자에서 기존 인스턴스 존재 여부를 확인해야 함
- 멀티 클래스로더 환경 : JVM에서 여러 클래스 로더가 같은 클래스의 서로 다른 인스턴스를 생성할 수 있음

**스레드 전용 싱글턴 패턴**

- 스레드마다 고유한 인스턴스를 갖는 싱글턴
- 멀티 스레드 환경에서 충돌 방지
- DB 트랜잭션 컨텍스트 (스레드별로 독립된 트랜잭션 유지)
- 사용자 세션 정보 저장 (Thread-local을 활용한 세션 분리)
- 로그 트레이싱 (스레드별 요청 ID 유지)

● **장점**

- 공유 자원 없이 스레드 안전하게 상태 유지 가능
- 전역 동기화 불필요 (성능 이점)

● **단점**

- 인스턴스가 스레드마다 생성되므로 메모리 소모가 증가
- 쓰레드 풀 환경에서는 재사용 쓰레드로 인해 상태가 누적될 수 있음 → remove() 필요

**클러스터 환경에서의 싱글턴 패턴**

- 클러스터 또는 분산 환경에서는 여러 JVM(노드)이 존재함.
- 이 경우 각 노드가 싱글턴 인스턴스를 따로 생성하므로 진정한 싱글턴이 아님.
- 전역 동기화 어려움 → 한 노드의 상태가 다른 노드에 반영되지 않음
- 데이터 불일치, 중복 처리, 레이스 컨디션 등의 문제 발생
1. 분산 캐시 활용 (예: Redis, Memcached)
    - 싱글턴처럼 사용할 객체를 Redis에 저장
    - 각 노드는 Redis에서 데이터를 가져와 사용
2. ZooKeeper 기반 분산 락
    - 리더 노드를 하나만 유지하는 방식
    - 특정 서비스 인스턴스 하나만 실제 작업 수행 (다른 인스턴스는 대기)
3. Redisson의 RedLock
    - Redis 기반 분산 락 구현체
    - 자바에서 클러스터 환경에서의 싱글턴 유지를 위한 라이브러리
4. Spring Cloud의 Leader Election
    - 마이크로서비스 중 하나를 리더로 선출하여 싱글턴 역할 부여
- 분산 스케줄러 (Quartz Cluster, Kubernetes CronJob)
- 분산 환경에서 유일한 작업 수행 (ex: DB 마이그레이션, 배치 수행자)

**다중 인스턴스 패턴**

- **키(key)에 따라 객체를 하나씩만 생성**하는 패턴
- "다중 싱글턴"이라고도 부르며, 키마다 단 하나의 인스턴스를 유지
- 메모리 절약 (필요한 키에 대해서만 인스턴스 생성)
- 키 기반 제어로 높은 유연성
- 캐시 누수 위험 (무한 키 사용 시 메모리 과부하)
- 키 관리 로직 필요 (적절한 키 설계가 중요)
- 스레드 동기화 필요 (특히 Java 환경에서 동시 접근 시)

**생각해보기**

- 이번절에서는 싱글턴 패턴에서 유일성의 범위가 프로세스에 한정된다고 언급했는데, 사실 Java의 경우 더 엄밀히 말하면 싱글턴 패턴의 유일성의 범위는 프로세스 안이 아닌 클래스 로더 안에 있다
