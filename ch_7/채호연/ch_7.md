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
## 7.2 데커레이터 패턴(WIP)

---
## 7.3 어댑터 패턴

### 1. 어댑터 패턴의 정의
- 어댑터 패턴은 호환되지 않는 인터페이스를 가진 클래스들이 함께 작동할 수 있도록 해주는 구조적 디자인 패턴.
- 기존 클래스의 인터페이스를 클라이언트가 기대하는 다른 인터페이스로 변환하는 역할 수행

**핵심 구성 요소:**
- **Target**: 클라이언트가 사용하고자 하는 인터페이스
- **Adaptee**: 어댑터가 감싸는 기존 클래스 (호환되지 않는 인터페이스)
- **Adapter**: Target 인터페이스를 구현하면서 Adaptee를 감싸는 클래스
- **Client**: Target 인터페이스를 사용하는 클라이언트

### 2. 어댑터 패턴의 구현 방안

#### 1) 클래스 어댑터 (상속 활용)
- 클래스 어댑터는 다중 상속을 지원하는 언어에서 사용 가능한 방식
- Java는 단일 상속만 지원하므로 인터페이스와 클래스를 조합하여 구현
**예시 코드:**

```java
// Target 인터페이스 - 클라이언트가 기대하는 인터페이스
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Adaptee - 기존의 호환되지 않는 클래스
class AdvancedMediaPlayer {
    public void playVlc(String fileName) {
        System.out.println("VLC 파일 재생: " + fileName);
    }
    
    public void playMp4(String fileName) {
        System.out.println("MP4 파일 재생: " + fileName);
    }
}

// 클래스 어댑터 - 상속과 인터페이스 구현을 함께 사용
class MediaAdapter extends AdvancedMediaPlayer implements MediaPlayer {
    
    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("vlc")) {
            super.playVlc(fileName);
        } else if (audioType.equalsIgnoreCase("mp4")) {
            super.playMp4(fileName);
        } else {
            System.out.println("지원하지 않는 형식: " + audioType);
        }
    }
}

// 사용 예시
public class ClassAdapterExample {
    public static void main(String[] args) {
        MediaPlayer player = new MediaAdapter();
        
        player.play("vlc", "movie.vlc");
        player.play("mp4", "video.mp4");
        player.play("avi", "clip.avi"); // 지원하지 않는 형식
    }
}
```

**클래스 어댑터의 특징:**
- **장점**: Adaptee의 모든 메서드에 직접 접근 가능, 오버라이드를 통한 세밀한 제어 가능
- **단점**: 다중 상속이 필요하여 언어 제약이 있음, 결합도가 높음

#### 2) 객체 어댑터 (합성 이용)
- 객체 어댑터는 합성을 사용하여 Adaptee 객체를 포함하는 방식
- Java에서 더 일반적으로 사용되는 방법

**예시 코드:**

```java
// Target 인터페이스
interface DrawingAPI {
    void drawCircle(double x, double y, double radius);
    void drawRectangle(double x, double y, double width, double height);
}

// Adaptee 1 - 기존 그래픽 라이브러리
class LegacyRectangle {
    private double x1, y1, x2, y2;
    
    public LegacyRectangle(double x1, double y1, double x2, double y2) {
        this.x1 = x1; this.y1 = y1;
        this.x2 = x2; this.y2 = y2;
    }
    
    public void draw() {
        System.out.println("레거시 사각형 그리기: (" + x1 + "," + y1 + ") to (" + x2 + "," + y2 + ")");
    }
    
    public void resize() {
        System.out.println("레거시 사각형 크기 조정");
    }
}

// Adaptee 2 - 다른 그래픽 라이브러리
class ThirdPartyCircleLib {
    public void renderCircle(int centerX, int centerY, int radius, String color) {
        System.out.println("써드파티 원 그리기: 중심(" + centerX + "," + centerY + 
                         "), 반지름=" + radius + ", 색상=" + color);
    }
}

// 객체 어댑터 1 - LegacyRectangle을 DrawingAPI로 변환
class RectangleAdapter implements DrawingAPI {
    private LegacyRectangle rectangle;
    
    public RectangleAdapter(LegacyRectangle rectangle) {
        this.rectangle = rectangle;
    }
    
    @Override
    public void drawCircle(double x, double y, double radius) {
        throw new UnsupportedOperationException("Rectangle adapter cannot draw circles");
    }
    
    @Override
    public void drawRectangle(double x, double y, double width, double height) {
        // LegacyRectangle의 좌표 시스템을 DrawingAPI 좌표 시스템으로 변환
        rectangle = new LegacyRectangle(x, y, x + width, y + height);
        rectangle.draw();
    }
}

// 객체 어댑터 2 - ThirdPartyCircleLib을 DrawingAPI로 변환
class CircleAdapter implements DrawingAPI {
    private ThirdPartyCircleLib circleLib;
    
    public CircleAdapter(ThirdPartyCircleLib circleLib) {
        this.circleLib = circleLib;
    }
    
    @Override
    public void drawCircle(double x, double y, double radius) {
        // double을 int로, 기본 색상 설정
        circleLib.renderCircle((int)x, (int)y, (int)radius, "black");
    }
    
    @Override
    public void drawRectangle(double x, double y, double width, double height) {
        throw new UnsupportedOperationException("Circle adapter cannot draw rectangles");
    }
}

// 통합 어댑터 - 여러 Adaptee를 하나의 인터페이스로 통합
class UnifiedGraphicsAdapter implements DrawingAPI {
    private ThirdPartyCircleLib circleLib;
    private Map<String, LegacyRectangle> rectangles;
    
    public UnifiedGraphicsAdapter() {
        this.circleLib = new ThirdPartyCircleLib();
        this.rectangles = new HashMap<>();
    }
    
    @Override
    public void drawCircle(double x, double y, double radius) {
        circleLib.renderCircle((int)x, (int)y, (int)radius, "blue");
    }
    
    @Override
    public void drawRectangle(double x, double y, double width, double height) {
        String id = "rect_" + x + "_" + y;
        LegacyRectangle rect = new LegacyRectangle(x, y, x + width, y + height);
        rectangles.put(id, rect);
        rect.draw();
    }
}

// 사용 예시
public class ObjectAdapterExample {
    public static void main(String[] args) {
        // 개별 어댑터 사용
        DrawingAPI rectAdapter = new RectangleAdapter(new LegacyRectangle(0, 0, 10, 20));
        DrawingAPI circleAdapter = new CircleAdapter(new ThirdPartyCircleLib());
        
        rectAdapter.drawRectangle(5, 5, 15, 25);
        circleAdapter.drawCircle(10, 10, 5);
        
        System.out.println("---");
        
        // 통합 어댑터 사용
        DrawingAPI unifiedAdapter = new UnifiedGraphicsAdapter();
        unifiedAdapter.drawCircle(20, 20, 8);
        unifiedAdapter.drawRectangle(30, 30, 40, 50);
    }
}
```

**객체 어댑터의 특징:**
- **장점**: 언어 제약이 없음, 런타임에 Adaptee 변경 가능, 느슨한 결합
- **단점**: Adaptee의 private 메서드에 접근 불가, 약간의 성능 오버헤드

### 3. 어댑터 패턴이 필요한 대표적 시나리오
#### 1) 레거시 시스템 통합
#### 2) 외부 라이브러리 통합
#### 3) 데이터베이스 드라이버 통합

```java
// 서로 다른 데이터베이스 드라이버들
class MySQLDriver {
    public void connectMySQL(String host, int port, String database) {
        System.out.println("MySQL 연결: " + host + ":" + port + "/" + database);
    }
    
    public String executeMySQLQuery(String sql) {
        return "MySQL 결과: " + sql;
    }
}

class PostgreSQLDriver {
    public boolean connectPostgres(String connectionString) {
        System.out.println("PostgreSQL 연결: " + connectionString);
        return true;
    }
    
    public Object runQuery(String query) {
        return "PostgreSQL 결과: " + query;
    }
}

// 통합 데이터베이스 인터페이스
interface DatabaseConnection {
    void connect(String connectionInfo);
    String executeQuery(String sql);
    void disconnect();
}

// MySQL 어댑터
class MySQLAdapter implements DatabaseConnection {
    private MySQLDriver driver;
    private boolean connected = false;
    
    public MySQLAdapter(MySQLDriver driver) {
        this.driver = driver;
    }
    
    @Override
    public void connect(String connectionInfo) {
        // "host:port/database" 형식 파싱
        String[] parts = connectionInfo.split("[:/]");
        driver.connectMySQL(parts[0], Integer.parseInt(parts[1]), parts[2]);
        connected = true;
    }
    
    @Override
    public String executeQuery(String sql) {
        if (!connected) throw new IllegalStateException("Not connected");
        return driver.executeMySQLQuery(sql);
    }
    
    @Override
    public void disconnect() {
        connected = false;
        System.out.println("MySQL 연결 해제");
    }
}

// PostgreSQL 어댑터
class PostgreSQLAdapter implements DatabaseConnection {
    private PostgreSQLDriver driver;
    private boolean connected = false;
    
    public PostgreSQLAdapter(PostgreSQLDriver driver) {
        this.driver = driver;
    }
    
    @Override
    public void connect(String connectionInfo) {
        connected = driver.connectPostgres("postgresql://" + connectionInfo);
    }
    
    @Override
    public String executeQuery(String sql) {
        if (!connected) throw new IllegalStateException("Not connected");
        return driver.runQuery(sql).toString();
    }
    
    @Override
    public void disconnect() {
        connected = false;
        System.out.println("PostgreSQL 연결 해제");
    }
}

// 사용 예시
public class DatabaseAdapterExample {
    public static void main(String[] args) {
        // MySQL 사용
        DatabaseConnection mysqlConn = new MySQLAdapter(new MySQLDriver());
        mysqlConn.connect("localhost:3306/mydb");
        System.out.println(mysqlConn.executeQuery("SELECT * FROM users"));
        mysqlConn.disconnect();
        
        System.out.println("---");
        
        // PostgreSQL 사용 (동일한 인터페이스)
        DatabaseConnection pgConn = new PostgreSQLAdapter(new PostgreSQLDriver());
        pgConn.connect("localhost:5432/mydb");
        System.out.println(pgConn.executeQuery("SELECT * FROM users"));
        pgConn.disconnect();
    }
}
```

---

## 7.4 브리지 패턴

### 1. 브리지 패턴의 정의
- 브리지 패턴은 추상화와 구현을 분리하여 각각을 독립적으로 변경할 수 있게 해주는 구조적 디자인 패턴
- 상속 대신 합성을 사용하여 클래스 계층 구조의 폭발적 증가를 방지
**핵심 아이디어**: "상속보다는 합성을 사용하라"는 객체지향 설계 원칙을 실현하는 패턴

### 2. 브리지 패턴의 목적: 폭발적 상속 해결하기
#### 2.1 예시: 차량을 표현하는 경우
> 시나리오
> - 선루프와 휠 허브라는 두 개의 선택사항(차원)이 있다고 가정. 
> - 각각 M, N 가지 선택지가 존재할 때, 상속을 사용하면 M×N 개의 하위 클래스를 정의해야 함. 
> - 하지만 브리지 패턴을 활용하면 합성 관계로 단순화하여 표현 가능

**문제 상황 - 상속을 사용한 경우:**

```java
// 폭발적 상속 문제 예시
abstract class Car {
    protected String brand;
    protected String model;
    
    public Car(String brand, String model) {
        this.brand = brand;
        this.model = model;
    }
    
    public abstract void start();
    public abstract void drive();
}

// 선루프 유무 × 휠 허브 종류의 조합으로 클래스 폭발!
class SedanWithSunroofAndAlloyWheels extends Car {
    public SedanWithSunroofAndAlloyWheels(String brand, String model) {
        super(brand, model);
    }
    
    @Override
    public void start() {
        System.out.println("선루프가 있는 알로이 휠 세단 시동");
    }
    
    @Override
    public void drive() {
        System.out.println("선루프를 열고 알로이 휠로 주행");
    }
}

class SedanWithSunroofAndSteelWheels extends Car {
    // 또 다른 조합...
}

class SedanWithoutSunroofAndAlloyWheels extends Car {
    // 또 다른 조합...
}

class SedanWithoutSunroofAndSteelWheels extends Car {
    // 또 다른 조합...
}

class SUVWithSunroofAndAlloyWheels extends Car {
    // SUV 타입의 모든 조합...
}

// 이런 식으로 계속... 선루프(2가지) × 휠 타입(3가지) × 차량 종류(4가지) = 24개 클래스!
```

**해결책 - 브리지 패턴 적용:**

```java
// Implementation 인터페이스들 - 구현 차원들을 분리
public class Car {
	private SunProof sunProof;
	private Hub hub;
	
	public Car(SunProof sunProof, Hub hub){
		this.sunProof = sunProof;
		this.hub = hub;
	}
}
```

**브리지 패턴의 장점:**
- **클래스 수 감소**: 상속 시 M×N개였던 클래스가 M+N개로 감소
- **런타임 변경**: 객체 생성 후에도 구현을 변경할 수 있음
- **독립적 확장**: 추상화와 구현을 독립적으로 확장 가능
- **플랫폼 독립성**: 다양한 플랫폼에서 동일한 추상화 사용 가능
---

## 7.5 파사드 패턴

### 1. 파사드 패턴의 정의

**GoF의 디자인 패턴에서의 정의:**
> "파사드 패턴은 서브 시스템에 대한 통합 인터페이스 세트를 제공하고, 하위 시스템을 더 쉽게 만들기 위한 상위 통합 인터페이스를 제공한다."
- 파사드 패턴은 복잡한 서브시스템의 복잡성을 숨기고, 클라이언트가 서브시스템과 상호작용할 수 있는 단순한 인터페이스를 제공하는 구조적 디자인 패턴

**핵심 특징:**
- 복잡한 서브시스템을 단순한 인터페이스로 감쌈
- 클라이언트와 서브시스템 간의 결합도를 낮춤
- 서브시스템의 사용을 단순화하지만, 서브시스템의 모든 기능을 숨기지는 않음
- 추가적인 기능을 제공하지 않고, 기존 기능의 접근을 간소화

**구성 요소:**
- **Facade**: 서브시스템의 복잡한 기능을 단순한 인터페이스로 제공
- **Subsystem Classes**: 실제 작업을 수행하는 복잡한 서브시스템들
- **Client**: 파사드를 통해 서브시스템을 사용하는 클라이언트

### 2. 파사드 패턴의 응용
#### 1) 인터페이스 사용성 개선하기
**Linux 운영체제의 호출 기능 예시:**
- Linux 운영체제의 시스템 콜은 일종의 파사드 계층
- 개발자를 위해 제공하는 편리한 프로그래밍 인터페이스 묶음으로, 실제 Linux 운영체제의 커널 호출을 캡슐화한 것.

```java
// 복잡한 서브시스템들 - 실제 커널 레벨 작업들
class FileSystemManager {
    public void createInode(String path, int permissions) {
        System.out.println("커널: inode 생성 - " + path + " (권한: " + permissions + ")");
    }
    
    public void allocateBlocks(String path, long size) {
        System.out.println("커널: 디스크 블록 할당 - " + path + " (" + size + " bytes)");
    }
    
    public void updateDirectoryEntry(String parentDir, String fileName) {
        System.out.println("커널: 디렉토리 엔트리 업데이트 - " + parentDir + "/" + fileName);
    }
}

class MemoryManager {
    public long allocateVirtualMemory(long size) {
        System.out.println("커널: 가상 메모리 할당 - " + size + " bytes");
        return System.currentTimeMillis(); // 메모리 주소 시뮬레이션
    }
    
    public void mapPhysicalMemory(long virtualAddr, long physicalAddr) {
        System.out.println("커널: 물리 메모리 매핑 - " + virtualAddr + " -> " + physicalAddr);
    }
    
    public void setMemoryProtection(long addr, int protection) {
        System.out.println("커널: 메모리 보호 설정 - " + addr + " (보호: " + protection + ")");
    }
}

class ProcessManager {
    public int createProcessControlBlock(String processName) {
        int pid = (int)(Math.random() * 10000);
        System.out.println("커널: PCB 생성 - " + processName + " (PID: " + pid + ")");
        return pid;
    }
    
    public void scheduleProcess(int pid, int priority) {
        System.out.println("커널: 프로세스 스케줄링 - PID " + pid + " (우선순위: " + priority + ")");
    }
    
    public void setProcessState(int pid, String state) {
        System.out.println("커널: 프로세스 상태 변경 - PID " + pid + " -> " + state);
    }
}

class SecurityManager {
    public boolean checkPermissions(String user, String resource, String action) {
        System.out.println("커널: 권한 확인 - " + user + " -> " + resource + " (" + action + ")");
        return Math.random() > 0.1; // 90% 성공률
    }
    
    public void auditLog(String user, String action, boolean success) {
        System.out.println("커널: 감사 로그 - " + user + " " + action + " " + (success ? "성공" : "실패"));
    }
}

// 파사드 - 리눅스 시스템 콜 인터페이스
class LinuxSystemCallFacade {
    private FileSystemManager fileSystem;
    private MemoryManager memory;
    private ProcessManager process;
    private SecurityManager security;
    
    public LinuxSystemCallFacade() {
        this.fileSystem = new FileSystemManager();
        this.memory = new MemoryManager();
        this.process = new ProcessManager();
        this.security = new SecurityManager();
    }
    
    // 파일 생성 - 복잡한 커널 작업들을 단순한 인터페이스로 제공
    public boolean createFile(String path, String user, long size) {
        System.out.println("=== 시스템 콜: 파일 생성 ===");
        
        // 1. 권한 확인
        if (!security.checkPermissions(user, path, "CREATE")) {
            security.auditLog(user, "CREATE_FILE", false);
            return false;
        }
        
        // 2. 파일 시스템 작업
        fileSystem.createInode(path, 644);
        fileSystem.allocateBlocks(path, size);
        
        String parentDir = path.substring(0, path.lastIndexOf('/'));
        String fileName = path.substring(path.lastIndexOf('/') + 1);
        fileSystem.updateDirectoryEntry(parentDir, fileName);
        
        // 3. 감사 로그
        security.auditLog(user, "CREATE_FILE", true);
        
        System.out.println("파일 생성 완료: " + path);
        return true;
    }
    
    // 프로세스 실행 - 복잡한 프로세스 관리를 단순화
    public int executeProgram(String programPath, String user, int priority) {
        System.out.println("=== 시스템 콜: 프로그램 실행 ===");
        
        // 1. 권한 확인
        if (!security.checkPermissions(user, programPath, "EXECUTE")) {
            security.auditLog(user, "EXECUTE", false);
            return -1;
        }
        
        // 2. 메모리 할당
        long memAddr = memory.allocateVirtualMemory(1024 * 1024); // 1MB
        memory.mapPhysicalMemory(memAddr, memAddr + 0x1000);
        memory.setMemoryProtection(memAddr, 5); // READ | EXECUTE
        
        // 3. 프로세스 생성
        int pid = process.createProcessControlBlock(programPath);
        process.scheduleProcess(pid, priority);
        process.setProcessState(pid, "READY");
        
        // 4. 감사 로그
        security.auditLog(user, "EXECUTE", true);
        
        System.out.println("프로그램 실행 완료: " + programPath + " (PID: " + pid + ")");
        return pid;
    }
    
    // 메모리 맵 파일 - 복잡한 메모리 관리를 단순화
    public long mapFile(String filePath, String user, long size) {
        System.out.println("=== 시스템 콜: 파일 메모리 매핑 ===");
        
        if (!security.checkPermissions(user, filePath, "READ")) {
            security.auditLog(user, "MMAP", false);
            return -1;
        }
        
        long virtAddr = memory.allocateVirtualMemory(size);
        memory.mapPhysicalMemory(virtAddr, virtAddr + 0x2000);
        memory.setMemoryProtection(virtAddr, 3); // READ | WRITE
        
        security.auditLog(user, "MMAP", true);
        
        System.out.println("파일 메모리 매핑 완료: " + filePath + " -> 0x" + Long.toHexString(virtAddr));
        return virtAddr;
    }
}

// 사용 예시 - 개발자 관점
public class LinuxSystemCallExample {
    public static void main(String[] args) {
        LinuxSystemCallFacade system = new LinuxSystemCallFacade();
        
        // 복잡한 커널 작업들이 단순한 메서드 호출로 추상화됨
        system.createFile("/home/user/document.txt", "user", 1024);
        System.out.println();
        
        int pid = system.executeProgram("/bin/ls", "user", 10);
        System.out.println();
        
        long mappedAddr = system.mapFile("/home/user/data.bin", "user", 4096);
        System.out.println();
        
        // 개발자는 커널의 복잡한 내부 구조를 알 필요 없이
        // 간단한 시스템 콜 인터페이스만 사용하면 됨
    }
}
```

#### 2) 독립된 트랜잭션이 원자적으로 이루어지도록 하기

**가상화폐 거래 서비스 예시:**
- 회원 가입과 동시에 해당 회원의 지갑 데이터를 생성해야 하는 상황에서 파사드가 두 트랜잭션이 원자적으로 이루어지도록 하는 예시

```java
// 서브시스템 1: 사용자 관리 시스템
class UserManagementService {
    private Map<String, User> users = new HashMap<>();
    
    public User createUser(String email, String password, String name) throws Exception {
        System.out.println("사용자 생성 시작: " + email);
        
        if (users.containsKey(email)) {
            throw new Exception("이미 존재하는 사용자: " + email);
        }
        
        // 데이터베이스 저장 시뮬레이션
        simulateSlowOperation("사용자 DB 저장", 1000);
        
        User user = new User(generateUserId(), email, password, name);
        users.put(email, user);
        
        System.out.println("사용자 생성 완료: " + user.getId());
        return user;
    }
    
    public void deleteUser(String userId) throws Exception {
        System.out.println("사용자 삭제: " + userId);
        
        User userToDelete = null;
        for (User user : users.values()) {
            if (user.getId().equals(userId)) {
                userToDelete = user;
                break;
            }
        }
        
        if (userToDelete != null) {
            users.remove(userToDelete.getEmail());
            simulateSlowOperation("사용자 DB 삭제", 500);
            System.out.println("사용자 삭제 완료: " + userId);
        }
    }
    
    public User getUser(String userId) {
        return users.values().stream()
                .filter(user -> user.getId().equals(userId))
                .findFirst()
                .orElse(null);
    }
    
    private String generateUserId() {
        return "USER_" + System.currentTimeMillis();
    }
    
    private void simulateSlowOperation(String operation, int millis) {
        System.out.println("  " + operation + " 중...");
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// 서브시스템 2: 지갑 관리 시스템
class WalletManagementService {
    private Map<String, Wallet> wallets = new HashMap<>();
    
    public Wallet createWallet(String userId, String currency) throws Exception {
        System.out.println("지갑 생성 시작: " + userId + " (" + currency + ")");
        
        if (wallets.containsKey(userId + "_" + currency)) {
            throw new Exception("이미 존재하는 지갑: " + userId + "_" + currency);
        }
        
        // 블록체인 지갑 생성 시뮬레이션
        simulateSlowOperation("블록체인 지갑 생성", 2000);
        
        Wallet wallet = new Wallet(generateWalletId(), userId, currency, generateWalletAddress());
        wallets.put(userId + "_" + currency, wallet);
        
        System.out.println("지갑 생성 완료: " + wallet.getId());
        return wallet;
    }
    
    public void deleteWallet(String walletId) throws Exception {
        System.out.println("지갑 삭제: " + walletId);
        
        Wallet walletToDelete = null;
        String keyToDelete = null;
        
        for (Map.Entry<String, Wallet> entry : wallets.entrySet()) {
            if (entry.getValue().getId().equals(walletId)) {
                walletToDelete = entry.getValue();
                keyToDelete = entry.getKey();
                break;
            }
        }
        
        if (walletToDelete != null) {
            // 잔액 확인
            if (walletToDelete.getBalance().compareTo(BigDecimal.ZERO) > 0) {
                throw new Exception("잔액이 남아있는 지갑은 삭제할 수 없습니다: " + walletId);
            }
            
            wallets.remove(keyToDelete);
            simulateSlowOperation("블록체인 지갑 삭제", 1500);
            System.out.println("지갑 삭제 완료: " + walletId);
        }
    }
    
    public List<Wallet> getUserWallets(String userId) {
        return wallets.values().stream()
                .filter(wallet -> wallet.getUserId().equals(userId))
                .collect(Collectors.toList());
    }
    
    private String generateWalletId() {
        return "WALLET_" + System.currentTimeMillis();
    }
    
    private String generateWalletAddress() {
        return "0x" + Long.toHexString(System.currentTimeMillis()) + "abcd";
    }
    
    private void simulateSlowOperation(String operation, int millis) {
        System.out.println("  " + operation + " 중...");
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// 서브시스템 3: 알림 서비스
class NotificationService {
    public void sendWelcomeEmail(String email, String name) {
        System.out.println("환영 이메일 발송: " + email + " (" + name + ")");
        simulateSlowOperation("이메일 발송", 800);
    }
    
    public void sendWalletCreationNotification(String email, String walletAddress) {
        System.out.println("지갑 생성 알림 발송: " + email + " - " + walletAddress);
        simulateSlowOperation("알림 발송", 500);
    }
    
    public void sendAccountDeletionNotification(String email) {
        System.out.println("계정 삭제 알림 발송: " + email);
        simulateSlowOperation("알림 발송", 300);
    }
    
    private void simulateSlowOperation(String operation, int millis) {
        System.out.println("  " + operation + " 중...");
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// 서브시스템 4: 트랜잭션 관리자
class TransactionManager {
    private static class Transaction {
        private String id;
        private List<Runnable> operations = new ArrayList<>();
        private List<Runnable> rollbackOperations = new ArrayList<>();
        private boolean committed = false;
        
        public Transaction() {
            this.id = "TXN_" + System.currentTimeMillis();
        }
        
        public void addOperation(Runnable operation, Runnable rollback) {
            operations.add(operation);
            rollbackOperations.add(0, rollback); // 역순으로 추가
        }
        
        public void commit() throws Exception {
            System.out.println("트랜잭션 시작: " + id);
            
            try {
                for (Runnable operation : operations) {
                    operation.run();
                }
                committed = true;
                System.out.println("트랜잭션 커밋 완료: " + id);
                
            } catch (Exception e) {
                System.out.println("트랜잭션 실패, 롤백 수행: " + id);
                rollback();
                throw new Exception("트랜잭션 실패: " + e.getMessage());
            }
        }
        
        private void rollback() {
            for (Runnable rollbackOp : rollbackOperations) {
                try {
                    rollbackOp.run();
                } catch (Exception e) {
                    System.out.println("롤백 오류: " + e.getMessage());
                }
            }
            System.out.println("롤백 완료: " + id);
        }
    }
    
    public Transaction createTransaction() {
        return new Transaction();
    }
}

// 파사드 - 가상화폐 거래소 서비스
class CryptoExchangeFacade {
    private UserManagementService userService;
    private WalletManagementService walletService;
    private NotificationService notificationService;
    private TransactionManager transactionManager;
    
    public CryptoExchangeFacade() {
        this.userService = new UserManagementService();
        this.walletService = new WalletManagementService();
        this.notificationService = new NotificationService();
        this.transactionManager = new TransactionManager();
    }
    
    // 원자적 회원가입 - 사용자 생성 + 기본 지갑 생성이 모두 성공하거나 모두 실패
    public UserRegistrationResult registerUser(String email, String password, String name, 
                                              List<String> currencies) {
        System.out.println("\n=== 회원가입 프로세스 시작 ===");
        
        TransactionManager.Transaction transaction = transactionManager.createTransaction();
        
        User createdUser = null;
        List<Wallet> createdWallets = new ArrayList<>();
        
        try {
            // 1. 사용자 생성 작업 추가
            transaction.addOperation(
                () -> {
                    try {
                        createdUser = userService.createUser(email, password, name);
                    } catch (Exception e) {
                        throw new RuntimeException(e);
                    }
                },
                () -> {
                    if (createdUser != null) {
                        try {
                            userService.deleteUser(createdUser.getId());
                        } catch (Exception e) {
                            System.out.println("사용자 롤백 실패: " + e.getMessage());
                        }
                    }
                }
            );
            
            // 2. 각 통화별 지갑 생성 작업 추가
            for (String currency : currencies) {
                transaction.addOperation(
                    () -> {
                        try {
                            Wallet wallet = walletService.createWallet(createdUser.getId(), currency);
                            createdWallets.add(wallet);
                        } catch (Exception e) {
                            throw new RuntimeException(e);
                        }
                    },
                    () -> {
                        // 생성된 지갑들 삭제
                        for (Wallet wallet : new ArrayList<>(createdWallets)) {
                            try {
                                walletService.deleteWallet(wallet.getId());
                                createdWallets.remove(wallet);
                            } catch (Exception e) {
                                System.out.println("지갑 롤백 실패: " + e.getMessage());
                            }
                        }
                    }
                );
            }
            
            // 3. 알림 발송 작업 추가
            transaction.addOperation(
                () -> {
                    notificationService.sendWelcomeEmail(email, name);
                    for (Wallet wallet : createdWallets) {
                        notificationService.sendWalletCreationNotification(email, wallet.getAddress());
                    }
                },
                () -> {
                    // 알림은 롤백할 필요 없음 (이미 발송된 것은 취소 불가)
                    System.out.println("알림 발송 롤백 (실제로는 수행되지 않음)");
                }
            );
            
            // 모든 작업을 원자적으로 실행
            transaction.commit();
            
            System.out.println("=== 회원가입 성공 ===");
            return new UserRegistrationResult(true, createdUser, createdWallets, null);
            
        } catch (Exception e) {
            System.out.println("=== 회원가입 실패 ===");
            return new UserRegistrationResult(false, null, null, e.getMessage());
        }
    }
```

**파사드 패턴의 실제 장점:**
- **복잡성 숨김**: 클라이언트는 4개의 서브시스템을 개별적으로 다룰 필요 없이, 파사드의 간단한 메서드만 호출하면 됨
- **원자성 보장**: 여러 서브시스템에 걸친 작업이 모두 성공하거나 모두 실패하도록 트랜잭션 관리
- **일관된 인터페이스**: 복잡한 비즈니스 로직을 일관된 방식으로 제공
- **오류 처리 통합**: 각 서브시스템의 다양한 예외를 통합적으로 처리
