# 디자인패턴 스터디 : Ch4_코딩 규칙

## 챕터 내용 개요

* 클래스 혹은 변수명은 긴 것이 좋을까, 짧은 것이 좋을까?
* 클래스/변수명을 '모두가 다 이해할 수 있도록' 단순화하는 방법이 있을까?
* 명명은 정확하지만 추상적이어야한다.
* 주석은 어떤 내용을 포함해야하는가?
* 클래스와 함수의 적절한 크기란 어느 정도일까?
* 필연적으로 길 수 밖에 없는 클래스와 함수라면, 어떻게 가독성을 높일 수 있을까?
* 복잡한 코드를 어떻게 단순화할 수 있을까?

## I. 명명과 주석

### 1. 명명
* 긴 이름과 짧은 이름 중 어느 한 쪽이 절대적으로 정답이 아니다. 결국 상황에 따라 다르다.
* 하지만 통상적으로 둘 중 어느 쪽을 선택하는 쪽이 유리한지 선택하기에 도움이 되는 기준은 존재한다.
   * 사용 범위가 비교적 좁은 변수 및 함수(ex. 함수에서 값을 복사할 때 사용하는 temp)는 a, b, c 와 같이 짧은 쪽을 사용할 수 있다.
   * 사용 범위가 큰 경우(ex. 전역 변수) 혹은 클래스 멤버 변수와 같이 의미가 분명하고 모두가 알아야할 것 => '이해하기 충분한 길이'를 갖도록 한다.

### 2. 클래스/변수명을 모두가 다 이해할 수 있도록 단순화하는 방법이 있을까?
1. 문맥 정보를 사용한 명명 단순화.
```java
public class User {
   private String userName; // 생략해도 무방.
}
```
2. 위와 같이 클래스 혹은 해당 변수의 정의 위치 및 사용 맥락이 분명하여 추론이 어렵지 않은 경우, 배제해도 괜찮다.
3. 비즈니스 용어집(DDD 에서의 Ubiquitous Language)을 사용한 명명 통일 - 팀 단위 작업 시, 해당 팀이 작업하고 있는 '도메인' 에서 사용되는 용어 및 이에 따른 변수명을 다 같이 협의하여 용어집을 만든다.
   * 이렇게 할 경우, 모두가 같은 개념에 대해 같은 용어를 사용할 수 있으므로 불필요한 소통 비용을 줄일 수 있다.
   * 이러한 용어집과 대응하는 개념이 DDD 의 Ubiquitous Language.

### 3. 명명은 정확하지만 추상적이어야 한다.
* 명명이 구현 세부사항을 너무 구체적으로 명시하지 않도록 할 것. 대신, 작업 방향 혹은 작업 자체가 무엇인지 암시하여 이해하기에 충분한 명명이 좋다.
   * ex. 명명이 구현 세부사항과 지나치게 밀접한 경우, 구현이 바뀔 때마다 변수명이 바뀌는 대참사가 발생할 수 있다.

### 4. 주석에 반드시 포함되어야하는 것들
* 주석에 포함되어야 하는 내용이 무엇인지, 커야 하는지 작아야하는지에 대한 의견이 분분.
* 하지만, 통상 '무엇을 하는지(what)', '왜 하는지(why)', '어떻게 하는지(how)' 와 같은 내용을 포함하는 것이 적절하다고 다음의 세 가지 근거를 들며 저자는 말한다!
   1. 주석에는 이름보다 훨씬 더 많은 정보를 담을 수 있다. - 통상 클래스는 메서드보다 담고 있는 내용이 더 많다. 그렇기에 클래스의 명명만으로는 클래스의 역할과 목적을 충분히 설명하기 어렵다. 이때 주석을 활용하는 것이 가독성 측면에서 유익하다.
   2. 주석은 설명과 시연의 역할을 한다.
      * 복잡한 클래스 혹은 인터페이스의 경우, 간단한 사용 예제를 주석에 담아 어떻게 사용하는지 정확하게 작성할 수 있다.
   3. 요약 주석은 코드의 논리를 더 명확하게 할 수 있다.
      * 복잡한 논리를 가진 함수를 단일 책임 기반으로 분할하는 것이 어려운 경우, 이에 대한 요약을 주석으로 표현하여 분할 불가로부터 염려할 수 있는 가독성 저하를 보충할 수 있다!

## II. 코드 스타일

### 1. 클래스/함수의 적절한 크기
* 사람마다 기준이 다르다. 하지만 다음과 같은 신호가 보인다면 지나치게 클 수 있음을 의심해보자!
   * 1) 클래스 코드를 읽기 어렵다고 호소하는 팀원이 1인 이상 존재한다!
   * 2) 클래스 크기 대비 실제 클래스 내부의 함수 중 사용하는 것이 지나치게 적다.
   * 3) 클래스 내부에서 어떤 함수를 사용해야 하는지 찾기 어려운 경우

### 2. 코드 한 줄의 적절한 길이
* Google 의 코드 컨벤션은 Java 로 작성된 프로그램의 코드 한 줄이 100자 이내일 것을 요구한다.
* 위의 구글 컨벤션이 절대적인 것은 아니나, 통상 코드를 다 읽기 위해 IDE 의 스크롤을 움직여야 한다면 줄이는 것을 고려해보자!

### 3. 빈 줄을 활용한 코드 블록 구분
* 그럼에도 불구하고 어쩔 수 없이 긴 메서드 / 클래스 등이 존재할 수 있다.
* 이 경우 빈 줄을 활용하여 논리적 단위로 구분되어보이도록 해보자!

### 4. 4칸 들여쓰기 v.s 2칸 들여쓰기
* 프로그래밍 언어마다 상이한 컨벤션을 사용한다. 하지만 Java 는 다른 언어보다 조금 긴 편이기 때문에 두 줄 들여쓰기가 유리하다고 저자는 생각한다!
* 다만, 절대 TAB 을 사용하지 말 것! -> IDE 마다 Tab 입력 시 적용되는 들여쓰기 간격 크기가 달라질 수 있기 때문.

### 5. 클래스의 멤버 순서
* Google 의 경우 의존 클래스(저서에는 import 문을 통해 가져온 외부 클래스를 '종속' 클래스라고 부르나 오타인 것으로 사료된다.)를 알파벳순으로 정렬하도록 한다.
* 이 외에도 정적 변수와 비정적 변수, 접근 제어 범위에 따라 메서드를 정렬함으로써 가독성을 재고해보는 방법도 존재한다!

## III. 코딩 팁 : 프로그램을 작성할 때 참고해볼 수 있는 팁!

### 1. 복잡한 코드에 모듈화를 도입하여 가독성을 높여보자!
* 리팩터 전
```java
public void invest(long userId, long financialProductId){
   Calendar calendar = Calendar.getInstance();
   calendar.setTime(date);
   calendar.set(Calendar.DATE, (calendar.get(Calendar.DATE) + 1));
   if(calendar.get(Calendar.DAY_OF_MONTH) == 1){return;}
}
```
* 리팩터 후
```java
public void invest(long userId, long financialProductId){
   if(isLastDayOfMonth(new Date())){return;}
}

public boolean isLastDayOfMonth(Date date){…}
```

### 2. 함수의 매개 변수 중 'flag' 를 제거해보자.
* 이들은 논리적으로 별개의 함수로 각각 정의되어야할 가능성이 높다.
* 따라서 flag 대신, true/false 에 따라 다르게 작동하는 별개의 함수를 정의해보자!
