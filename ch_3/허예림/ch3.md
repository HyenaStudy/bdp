## Chapter 3 설계 원칙

### 3.1 단일 책임 원칙

- 클래스와 모듈은 하나의 책임 또는 기능만을 가지고 있어야 한다는 설계 원칙
- 거대하고 포괄적인 클래스/모듈 대신 작은 단위와 단일 기능을 가진 설계를 해야함
- 비즈니스와 관련 없는 기능이 두 개 이상 포함되어 있으면 단일하지 않음

**클래스에 단일 책임이 있는지 판단하는 방법**

예1) UserInfo 클래스

```java
public class UserInfo {
	private long userId;
	private String username;
	private String email;
	private String telephone;
	private long createTime;
	private long lastLoginTime;
	private String avatarUrl;
	private String provinceOfAddress;
	private String cityOfAddress;
	private String regionOfAddress;
	private String detailAddress;
}
```

- 관점 1 : UserInfo 클래스에는 사용자와 관련된 정보만 포함되어 있어 단일함
- 관점 2: UserInfo 클래스에서 주소 정보의 비율이 상대적으로 높고 정보를 독립적인 UserAddress 클래스로 분할 가능
- → 시나리오에 따라서 어떤 관점이 타당한지가 다를 수 있다.

**단일 책임 결정 원칙**

1. 클래스에 코드, 함수 또는 속성이 너무 많아 코드의 가독성과 유지보수성에 영향을 미치는 경우 클래스 분할을 고려해야 한다.
2. 클래스가 너무 과하게 다른 클래스에 의존한다면, 높은 응집도와 낮은 결합도의 코드 설계 사상에 부합하지 않으므로 클래스 분할을 고려해야 한다.
3. 클래스에 private 메서드가 너무 많은 경우 이 private 메서드를 새로운 클래스로 분리하고 더 많은 클래스에서 사용할 수 있도록 public 메서드로 설정하여 코드의 재사용성을향상시켜야 한다.
4. 클래스의 이름을 비즈니스적으로 정확하게 지정하기 어렵거나 Manager, Context 처럼 일반적인 단어가 아니면 클래스의 이름을 정의하기 어려울 경우, 클래스 책임 정의가 충분히 명확하지 않음을 의미할 수 있다.
5. UserInfo 클래스의 많은 메서드가 주소를 위해서만 구현된 앞의 예시처럼 클래스의 많은 메서드가 여러 속성 중 일부에서만 작동하는 경우 이러한 속성과 해당 메서드를 분할하는 것을 고려할 수 있다.

**클래스의 책임이 가능한 한 자세하게 설명되어 있는 지 여부**

```java
/**
	* Protocol format: identifier-string;{gson stirng}
	* For example: UEUEUE;{"a": "A", "b": "B"}
*/
public class Serialization {
	private static final Sring IDENTIFIER_STRING = "UEUEUE;";
	private Gson gson;
}

public Serialization() {
	this.gson = new Gson();
}

public String serialize(Map<String, String> object) {
	StringBuilder textBuilder = new StringBuilder();
	textBuilder.append(IDENTIFIER_STRING);
	textBuilder.append(gson.toJson(object));
	return textBuilder.toString();
}

public Map<String, String> deserialize(String text) {
		if (!text.startsWith(IDENTIFIER_STRING)) {
			return Collections.emptyMap();
		}
		String gsonStr = text.substring(IDENTIFIER_STRING.length());
		return gson.fromJson(gsonStr, Map.class);
		}
	}
```

여기서 Serialization 클래스의 책임을 더 세분화하려면 직렬화를 담당하는 Serializer 클래스와 역직렬화를 담당하는 Deserializer클래스로 나눌 수 있다.

```java
// Serializer.java
public class Serializer {
    private static final String IDENTIFIER_STRING = "UEUEUE;";
    private Gson gson;

    public Serializer() {
        this.gson = new Gson();
    }

    public String serialize(Map<String, String> object) {
        StringBuilder textBuilder = new StringBuilder();
        textBuilder.append(IDENTIFIER_STRING);
        textBuilder.append(gson.toJson(object));
        return textBuilder.toString();
    }
}

// Deserializer.java
public class Deserializer {
    private static final String IDENTIFIER_STRING = "UEUEUE;";
    private Gson gson;

    public Deserializer() {
        this.gson = new Gson();
    }

    public Map<String, String> deserialize(String text) {
        if (!text.startsWith(IDENTIFIER_STRING)) {
            return Collections.emptyMap();
        }
        String gsonStr = text.substring(IDENTIFIER_STRING.length());
        return gson.fromJson(gsonStr, Map.class);
    }
}
```

- 각 클래스가 책임은 단일화 되었지만 응집력이 떨어진다.
- 데이터 식별자를 수정한다거나, 직렬화 메서드를 수정하는 등 둘 중 하나의 클래스를 수정하면 다른 클래스도 수정해야 함

### 3.2 개방 폐쇄 원칙 Open-Closed Principle

- 확장할 때는 개방, 수정할 때는 폐쇄

**확장할때는 개방, 수정할 때는 폐쇄**

- 모듈, 클래스, 함수와 같은 소프트웨어 단위들은 확장을 위해 개발되어야 하지만 수정을 위해서는 폐쇄되어야 한다.
- 새로운 기능을 추가할때 기존의 모듈, 클래스, 함수를 수정하기보다는 기존의 코드를 기반으로 모듈, 클래스, 함수 등을 추가하는 방식으로 코드를 확장해야 한다는 뜻이다.

```java
public class Alert {
	private AlertRule rule;
	private Notification notification;
	
	public Alert(AlertRule rule, Notification notification) {
		this.rule = rule;
		this.notification = notification;
	}
	
	public abstract void check(ApiStafInfo apiStatInfo);
}

public class TpsAlertHandler extends AlertHandler {
	public TpsAlertHandler(AlertRule rule, Notification notification){
		super(rule, notification);
	}
	
	@Override
	public void check(ApiStatInfo apiStatInfo) {
	long tps = apiStatInfo.getRequestCount() / apiStatInfo.getDuration();
	if (tps > rule.getMatchedRule(apiStatInfo.getApi()).getMaxTps()) {
		notification.notify(NotificationEmergencyLevel.URGENCY, "...");
		}
	}
}

public class ErrorAlertHandler extends AlertHandler {
	public ErrorAlertHandler(AlertRule rule, Notification notification) {
		super(rule, notification);
	}
	
	@Override
	public void check(ApiStatInfo apiStatInfo) {
		if (apiStatInfo.getErrorCount() > 
		rule.getMatchedRule(apiStatInfo.getApi()).getMaxErrorCount()) {
			notification.notify(NotificationEmergencyLevel.SEVERE, "...");
		}
	}
}
```

리팩터링된 Alert 클래스

```java
// 조건 판단을 위한 인터페이스
public interface AlertCondition {
    boolean isTriggered(ApiStatInfo apiStatInfo, Rule matchedRule);
    NotificationEmergencyLevel getLevel();
    String getMessage();
}
// TPS 조건 판단 전략
public class TpsCondition implements AlertCondition {
    @Override
    public boolean isTriggered(ApiStatInfo apiStatInfo, Rule matchedRule) {
        long tps = apiStatInfo.getRequestCount() / apiStatInfo.getDuration();
        return tps > matchedRule.getMaxTps();
    }

    @Override
    public NotificationEmergencyLevel getLevel() {
        return NotificationEmergencyLevel.URGENCY;
    }

    @Override
    public String getMessage() {
        return "TPS threshold exceeded";
    }
}
// 에러 수 조건 판단 전략
public class ErrorCondition implements AlertCondition {
    @Override
    public boolean isTriggered(ApiStatInfo apiStatInfo, Rule matchedRule) {
        return apiStatInfo.getErrorCount() > matchedRule.getMaxErrorCount();
    }

    @Override
    public NotificationEmergencyLevel getLevel() {
        return NotificationEmergencyLevel.SEVERE;
    }

    @Override
    public String getMessage() {
        return "Error count threshold exceeded";
    }
}
// 예시 사용 코드
AlertHandler tpsHandler = new AlertHandler(alertRule, notification, new TpsCondition());
AlertHandler errorHandler = new AlertHandler(alertRule, notification, new ErrorCondition());

tpsHandler.check(apiStatInfo);
errorHandler.check(apiStatInfo);
```

- 이와 같이 리팩터링 한 코드가 더 유연하고 확장하기 쉽다.

**코드를 수정하는 것은 개방 폐쇄 원칙을 위반하는 것일까?**

- 변경사항 1 : timeoutCount 속성을 ApiStatInfo 클래스에 추가함 (getter, setter 포함) → 클래스에 새속성과 메서드를 추가하는 것은 수정에 해당할까 확장에 해당할까
    - 개방 폐쇄 원칙이 적용되는 단위에 따라 다를 수 있다.
    - 속성과 메서드가 추가되었기 때문에 클래스 입장에서는 수정이지만 메서드 입장에서는 확장이다.
- 변경사항 2,3 : ApplicationContext 클래스의 intializeBeans() 메서드에서 alert 객체에 TImeOoutAlertHandler를 등록하고, Alert 클래스를 사용할 때 check() 함수의 apiStatInfo 객체에 timeoutCount 속성 값 설정
    - 메서드 단위 내에서 변경이 이루어지기 때문에 확장이 아닌 수정으로 간주된다.
    - Alert 클래스와 그 핸들러 클래스를 모듈로 간주하면 모듈 수준에서 새 기능을 추가하기 위해 논리를 수정할 필요 없이 확장만 이루어진 것이라 볼 수 있다.

**확장할 떄는 개방, 수정할떄는 폐쇄를 달성하는 방법**

앞의 Alert 클래스 예시는 복잡한 케이스에 해당하며 개발 경험이 풍부하지 않을 경우 어려울 수 있다.

- 개방 폐쇄 원칙은 코드의 확장성 문제라고도 볼 수 있는데, 이는 코드가 확장하기 쉬운지를 판단하는 표준에 해당한다. 추후 변경되는 요구 사항에 대응할 때 코드가 확장할 때는 개방, 수정할때는 폐쇄될 수 있다면 코드의 확장성이 매우 뛰어나다는 것을 의미한다.
- 확장 가능한 코드를 작성하려면 확장, 추상화, 캡슐화에 대해 인식하고 있는 것이 매우 중요하다.
- 코드를 작성할 때 현재 코드에 앞으로 요구 사항이 추가될 가능성이 있는 지 판단하는 데 많은 시간을 할애할 필요가 있다.
- 코드의 변경 가능한 부분과 변경할 수 없는 부분을 잘 식별해야 한다. 변경되는 사항을 기존 코드와 분리할 수 있도록 변수 부분을 캡슐화하고, 상위 시스템에서 사용되는 변경되니 않을 추상 인터페이스를 제공해야 한다. 이 구조에서는 특정 구현이 변경되어도 추상 인터페이스를 기반으로 새로운 구현을 확장하여 기존 구현을 대체할 수 있으며, 상위 시스템의 코드를 수정할 필요가 없다.
- 코드의 확장성을 개선하기 위해 설계 원칙과 디자인 패턴에서 많이 사용되고 있는 방법에는 다형성, 의존성 주입, 구현이 아닌 인터페이스 기반의 프로그래밍이 있으며 이는 전략 패턴, 템플릿 메서드 패턴, 책임 연쇄 패턴과 같은 대부분의 디자인 패턴에서 볼 수 있다.

### 3.3 리스코프 치환 원칙

**리스코프 치환 원칙 정의**

- 하위 유형 또는 파생 클래스의 객체는 프로그램 내에서 상위 클래스가 나타나는 모든 상황에서 대체 가능하며, 프로그램이 원래 가지는 논리적인 동작이 변경되지 않으며 정확성도 유지돤다.

**리스코프 치환 원칙과 다형성 차이점**

- 공통점 : 둘 다 상속을 기반으로 한 개념으로 자식 클래스가 부모 클래스의 역할을 대신할 수 있도록 한다.
- 다형성 : 서로 다른 클래스의 객체가 같은 메시지를 받아도 다르게 동작할 수 있는 성질로 상속과 오버라이딩을 통해 여러 형태의 행동을 허용한다는 점, 코드를 유연하게 작성하고 여러 타입을 동일한 인터페이스로 다룰 수 있게 한다.
    
    ```python
    Animal a = new Dog();
    a.speak(); // Dog의 speak가 실행됨
    
    a = new Cat();
    a.speak(); // Cat의 speak가 실행됨
    ```
    
- 리스코프 치환 원칙 : 자식 클래스는 언제나 부모 클래스로 대체될 수 있어야한다. 즉, 자식이 부모의 계약을 깨지 않아야 한다는 것으로 프로그램의 안전성과 예측 가능성을 보장한다.

**리스코프 치환 원칙을 위배하는 안티 패턴**

- 계약에 따른 설계 : 하위 클래스를 설계할 떄는 상위 클래스의 동작 규칙을 따라야 한다. → 리스코프 치환 원칙과 동일
- 하위 클래스가 상위 클래스에서 선언한 기능을 위반하는 경우
- 하위 클래스가 입력, 출력 및 예외에 대한 상위 클래스의 계약을 위반하는 경우
- 하위 클래스가 상위 클래스의 주석에 나열된 특별 지침을 위반하는 경우



### 3.6 KISS 원칙과 YANGI 원칙

**KISS원칙 정의**

- 가능한한 단순하게 유지 Keep it Simple and Stupid
- 단순 소프트웨어 설계 뿐 아니라 제품 개발등 여러 분양에서 광범위하게 사용되는 핵심 원칙

**적은 줄 수의 코드가 더 간단하지 않다**

- 적은 줄 수의 코드가 더 어렵고 복잡해 이해하기 어려울 수 있다.
- 함수의 성능이 시스템 전체 성능을 떨어뜨리는 문제를 발생시키지 않는다면 굳이 코드 구현이 어렵고 가독성이 떨어지는 코드를 선택할 필요가 없다.

**복잡한 코드가 반드시 KISS 원칙을 위반하는 것은 아니다.**

**KISS 원칙을 만족하는 코드 작성법**

- 단순한 하나의 역할을 담은 함수 작성
- 중복 로직 제거하고 함수 분리
- 조건문과 반복문은 간단하게 작성
- 불필요한 추상화 지양
- 가독성을 고려한 네이밍 구조화

**YANGI와 KISS의 차이**

- You Aint gonna need it 원칙을 소프트웨어 개발에 적용하면 현재 사용되지 않는 기능을 설계하지 말고 현재 사용되지 않는 코드를 작성하지 않는다는 의미가 된다. KISS원칙은 가능한한 간단하게 유지하는 방법에 관한것이지만 YANGI 원칙은 현재 필요하지 않은 것을 미리 하지 말라는 금지에 관한 것이다.

### 3.7 DRY 원칙

- 소프트웨어 개발에서 사용되는 설계원칙 중 하나
1. 중복을 제거 : 비슷한 코드가 반복되면 잠재적인 버그가 존재하거나 코드 수정 시 많은 부분을 수정해야 하기 때문에 중복을 최소화 해야함
2. 추상화의 재사용 : 비슷한 동작을 하는 코드는 하나의 추상화된 구조로 정의하고 재사용할 수 있다.
    - 공통된 속성을 상속만으로 재사용 가능하게 함
    
    ```python
    class TimeStampedModel(models.Model):
        created_at = models.DateTimeField(auto_now_add=True)
        updated_at = models.DateTimeField(auto_now=True)
    
        class Meta:
            abstract = True
    ```
    
    - DRF의 Generic Views로 미리 기본 CRUD 동작을 구현해놓고 코드에서는 필요한 부분만 선택하여 동작하도록 추상화 제공
    
    ```python
    from rest_framework.generics import ListAPIView
    from .models import Product
    from .serializers import ProductSerializer
    
    # GET 메서드 동작
    class ProductListView(ListAPIView):
        queryset = Product.objects.all()
        serializer_class = ProductSerializer
    # GET 메서드 동작    
    class ProductListView(APIView):
        def get(self, request, *args, **kwargs):
            queryset = Product.objects.all()
            serializer = ProductSerializer(queryset, many=True)
            return Response(serializer.data)
    ```
    
3. 단일 진리 원칙 : 데이터 또는 비즈니스 규칙과 같은 정보는 하나의 소스에서 관리되어야 합니다. 중복된 정보가 존재할 경우, 정보가 일관되지 않을 수 있고, 수정이 용이하지 않을 수 있습니다. 
    - 하나의 [SETTINGS.PY](http://SETTINGS.PY) : 
    버전 관리 되지 않는 로컬 세팅을 피한다. , 운영에 필요 없는 설정이나 SECREYT_KEY와 같은 보안 관련 설정은 저장소에서 빼야하기 때문에 주로 local_settings라는 안티 패턴이 활용되었다. 이는 운영과 로컬의 환경을 다르게 하여 문제점을 파악하는데 어렵게 만들기도 하고 여리 팀원이 서로 local_settings.py를 복사해서 사용하는 것이 DRY원칙에 위배되는 사례이다. 따라서 한개의 settings.py를 이용한다기보다 settings 디렉터리 아래에 여러개의 셋업 파일을 구성한다.
- https://wiki.c2.com/?DontRepeatYourself
- DJango 설계 철학
    - Django's URL dispatcher: It allows you to define URL patterns and map them to corresponding views. By using named URLs and including URL patterns from other URL configurations, you can avoid hardcoding URLs in multiple places, promoting code reuse.
    - Template system: Django's template language supports template inheritance, allowing you to define base templates that contain common elements shared across multiple pages. Child templates can then extend the base template and provide specific content for each page, reducing the need for duplicate HTML code.
    - Model inheritance: Django's object-relational mapping (ORM) supports model inheritance, enabling you to define common fields and behaviors in a base model and derive specialized models from it. This approach helps avoid duplicating code and allows for code reuse and extensibility.
    - Middleware: Django's middleware framework lets you define reusable components that process requests and responses. Middleware can be used to handle common tasks such as authentication, session management, and caching, eliminating the need to repeat the same logic in multiple views.

**코드 논리의 중복**

- 코드가 논리는 동일하지만 의미가 다를 경우 DRY 원칙에 위배되지 않는다.
- 아래의 경우도 역할과 의미가 달라  DRY원칙에 위배되지 않는다고 볼 수 있다.
    
    ```python
    # before payments 전용
    cache_key = f"transaction_id_{transaction_id}"
    if cache.get(cache_key):
        cache.delete(cache_key)
        return Response(status=409, data={"message": "이미 처리된 요청입니다."})
    
    # after payments 전용
    cache_key = f"after_txid_{transaction_id}"
    if cache.get(cache_key):
        cache.delete(cache_key)
        return Response(status=409, data={"message": "이미 처리된 요청입니다."})
    
    ```
    

**DRY 원칙에 위배된 중복**

- 프로젝트에서 동일한 기능의 함수가 중복하여 존재하는 경우는 DRY 원칙에 위배된다고 할 수 있다.
- 코드의 논리적인 중복이나 의미적인 중복이 없더라도 실행에 중복이 있는 경우 DRY 원칙에 위배된다.

**코드 재사용성**

- 코드의 결합도를 줄인다
- 단일 책임 원칙을 충족시켜야 한다.
- 코드의 모듈화는 필수이다
- 비즈니스 논리와 비즈니스 논리가 아닌 부분을 분리할 필요가 있다.
- 일반적인 코드는 하위 계층으로 내려보낸다.
- 상속, 다셩성, 추상화, 캡슐화를 활용한다.
- 애플리케이션 템플릿과 같은 디자인 패턴을 활용하면 코드 재사용성을 향상시킬 수 있다.

**DRY원칙 주의 사항**

- DRY 원칙을 지나치게 적용하면 오히려 코드의 가독성과 유지보수성이 떨어질 수 있습니다.
- 이러한 문제를 피하기 위해 AHA(Avoid Hasty Abstraction)원칙이 제안되었습니다.
    - 성급한 추상화를 치하고 실제로 중복이 문제가 될 때까지 기다리자는 접근

### 3.8 LOD

**높은 응집도와 낮은 결합도에 대한 생각**

- 높은 응집도와 낮은 결합도는 코드의 가독성과 유지 보수성을 효과적으로 향상시키고 기능 변경으로 인한 코드 변경 범위를 줄일 수 있는 매우 중요한 설계사상이다.
- 높은 응집도는 클래스 자체의 설계에 사용된다. 유사항 기능은 동일한 클래스에 유사하지 않은 기능은 다른 클래스로 분리하여 배포해야한다.
- 낮은 결합도는 클래스 간의 의존성 설계에 사용되는데, 코드에서 클래스 간의 의존성이 단순하고 명확해야 함을 의미한다. 두 클래스가 종속 관계에 있을 때, 둘 중 어느 한쪽의 클래스를 수정하더라도 다른 클래스의 코드가거의 수정되지 않아야 한다.
- 응집도와 결합도는 완전히 독립적이지 않기 때문에 높은 응집도는 낮은 결합도를 이끌어 내고 반대도 마찬가지이다.

**LoD의 정의**

- LoD, 최소 지식의 원칙
- 자신이 직접 가지고 있거나 생성한 객체 또는 메서드 인자로 받은 객체의 메서드만 호출해야한다.
- 객체 간의 결합도를 낮추고 변경에 강한 구조를 만들기 위함

**직접 의존성이 없어야 함**

- 객체는 자신이 직접 소유하거나 반환받은 객체와만 상호작용하고, 그 내부의 객체와는 상호작용하지 않아야 한다.
- 위반 사례 :  `order.get_customer().get_address().get_zip_code()`  → `order.get_shipping_zip_code()`

**의존성이 있는 클래스는 필요한 인터페이스에만 의존**

- 어떤 클래스가 다른 클래스에 의존할 때 그 클래스가 제공하는 모든 기능에 의존해서는 안되고 정말 필요한 기능에만 의존해야 한다는 원칙
- 즉 인터페이스 단위로 의존을 최소화하라는 의미
- 위반 사례 (ReportService가 start와 stop의 시점을 알고 있을 필요 없음)
    
    ```python
    class Printer:
        def start(self): ...
        def stop(self): ...
        def print_document(self, doc): ...
    
    class ReportService:
        def __init__(self, printer: Printer):
            self.printer = printer
    
        def print_report(self, report):
            self.printer.start()
            self.printer.print_document(report)
            self.printer.stop()
    
    ```
