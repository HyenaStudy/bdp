**들여쓰기**
- 2칸 들여쓰기 권장 -> 코드 중첩이 늘어나면 문장이 2줄로 나뉠 수 있음
- Tab 키 사용 X → IDE마다 Tab키 너비 설정이 다름
<br>

**함수에서 플래그 매개변수 제거**
- true/false 실행하는 코드가 다르도록 설계 X → 단일 책임 원칙, 인터페이스 분리 원칙 위반  
 ==> 함수를 두 개로 분할
- 매개변수의 null여부로 내부 논리를 제어하면 분할 추천
- 영향 범위가 좁고 특정 목적에만 사용하거나 분할 후 두 개가 동시에 호출되는 경우가 많으면 플래그 유지
<br>

**설명 변수**
- 코드 가독성 향상, 불필요한 주석 감소
<br>

1. 매직넘버 대신 상수 사용
```java
// 매직넘버
public double CalculateCircularArea(double radius) {
  return (3.1415) * radius * radius;
}

// 상수
public static final Double PI = 3.1415;

public double CalculateCircularArea(double radius) {
  return PI * radius * radius;
}
```
<br>

2. 설명 변수로 복잡한 표현 설명
```java
if (month == 6 || month == 7 || month == 8) {
  print("더워")
}

// 설명변수 사용
boolean isSummer = (month == 6 || month == 7 || month == 8);

if (isSummer) {
  print("더워")
}
```
<br>