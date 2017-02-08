## 규칙5. 불필요한 객체는 만들지 말라

###변경 불가능 객체는 언제나 재사용할 수 있다. (규칙 15)
- 수정자 제공X, 계승X(final class), 필드를 final로 선언, private로 선언
- Thread-safe / 동기화 필요 없음 / 자유롭게 공유 가능

### 1. 생성자와 정적 팩터리 메서드를 함께 제공하는 변경 불가능 클래스의 경우, 정적 팩터리 메서드를 이용하라.


```JAVA
Boolean(String)
Boolean.valueOf(String)
```
- 아래의 것이 바람직하다.


### 2. 변경 가능한 객체도 재사용하라.

나쁜예
```JAVA
public class Person {
  private final Date birthDate;
  
  public boolean isBabyBoomer() {
    //생성 비용이 높은 객체를 쓸데없이 생성
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(~~);
    Date boomStart = gmtCal.getTime();
    ...
    
  }
}
```
좋은예
```JAVA
public class Person {
  private final Date birthDate;
  
  private static final Date BOOM_START;
  static {
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(~~);
    BOOM_START = gmtCal.getTime();
    ...
  }

  public boolean isBabyBoomer() {
    return birthDate.compareTo(BOOM_START) ...
  }
}
```

- Calendar, TimeZone, Date 객체를 클래스 초기화시 한 번만 만듦.
- 많이 불리지 않는 경우엔 초기화 지연 기법을 사용하면 피할 수 있지만, 복잡하며 성능을 개선하기도 어렵다.


###3. 쓸데없는 객체 생성 etc
- Map 인터페이스의 keySet 메서드는 Map 객체의 Set뷰를 반환 (Map : 후면객체 / Set : 뷰)
- Map의 keySet 호출시 매번 같은 Set을 반환
<br>
- Long -> long 이용
- 객체 표현형 대신 기본 자료형을 사용하고, 생각지도 못한 자동 객체화 발생하지 않도록 유의.

###4. 독자적으로 관리하는 객체 풀을 피하라
- 데이터베이스 연결은 객체 풀을 만드는 비용이 정당화 된다.
- 일반적으로는 코드가 어지러워지고, 메모리 요구량이 증가하며, 성능이 떨어짐.

###5. 규칙 39 - 새로운 객체를 만들어야 한다면 기존 객체는 재사용하지 말라 (TBD)
