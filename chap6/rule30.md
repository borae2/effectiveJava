## 규칙 30. int 상수 대신 enum을 사용하라

### 1. int enum 패턴은 캐지옥이다.
 ```JAVA
 public static final int APPLE_PIPPIN = 1;
 public static final int ORANGE_TEMPLE = 1;
 ```
  - 위처럼 하면 사과 대신 오렌지를 넣어도 된다.
  - int enum 상수는 컴파일 시점 상수이기 때문에 상수를 사용하는 클라이언트 코드와 함께 컴파일된다.
  - 변종으로 String enum도 있는데 이는 더 나쁘다.
    + 문자열 비교를 해야 하므로 성능이 나빠질 수 있다.


### 2. Enum 자료형
 - 하나의 객체를 public static final 필드 형태로 제공함.
 - enum 자료형은 실질적으로는 final로 선언된 것이나 마찬가지이다.(클라이언트가 접근할 수 있는 생성자가 없다.)
   + 계승을 통해 확장 X
   + 싱글턴 패턴을 일반화 한 것(싱글턴 패턴은 열거 상수가 하나뿐인 enum패턴)
 - Enum은 컴파일 시점 형 안전성을 제공한다. (Apple의 경우 Apple중 하나만)
 - 상수를 추가해도 다시 컴파일 할 필요 없음 - 상수를 제공하는 필드가 enum 자료형과 클라이언트 사이에서 격리 계층 구실을 하기 때문?

```JAVA
// 데이터와 연산을 구비한 enum 자료형
public enum Planet {
	MERCURY (3.302e+23, 2.439e6),
	VENUS   (4.234e+13, 6.042e5),
	EARTH   (5.923e+25, 6.371e5),
	...

	private final double mass;
	private final double radius;
	private final double surfaceGravity;

	// 중력 상수
	private static final double G = 6.67300E-11;

	// 생성자
	Planet(double mass, double radius) {
		this.mass = mass;
		this.radius = radius;
		surfaceGravity = G * mass / (radius * radius);
	}

	public double mass() { return mass; }
	public double raidus() { return radius; }
	public double surfaceGravity { return surfaceGravity; }

	public double surfaceWeight(double mass) {
		...
	}
}
```
  - enum상수에 데이터를 넣으려면 객체 필드(instance field)를 선언하고 생성자를 통해 받은 데이터를 그 필드에 저장하면 된다.
  - enum은 원래 변경불가하므로 final로 선언

```JAVA
public class WeightTable {
  public static void main(String[] args) {
    double earthWeight = Double.parseDouble(args[0]);
    double mass = earthWeight / Planet.EARTH.surfaceGravity();
    for (Planet p : Planet.values()) {
      System.out.println("Weight on %s is %f%n", p, p.surfaceWeight(mass));
    }
  }
}
```
```JAVA
Weight on MERCURY is 66.133672
Weight on VENUS is 158.383926
```
 - 패키지 안에서만 사용해야하면 private이나 package-private로 enum을 선언하라.

### 3. 상수들이 제각기 행동해야 하는 경우
 - enum 자료형에 abstract 메서드를 선언하고, 각 상수별 클래스 몸체 안에서 실제 메서드로 재정의 함
 ```JAVA
 public enum Operation {
   PLUS {double apply(double x, double y) {return x+y;}},
   MINUS {double apply(double x, double y) {return x-y;}};

   abstract double apply(double x, double y);
 }
 ```
  - 위와 같이 하면 새로운 상수를 추가할 때 abstract 메서드를 까먹을 일이 없다.

### 4. enum 자료형에 대한 fromString 메서드 구현
 - enum 자료형에는 자동 생성된 valueOf(String) 메서드가 있다.(상수의 이름을 상수 그 자체로 변환)
 - toString 메서드를 재정의 할 경우에는 fromString 메서드를 작성해서 toString이 뱉어내는 문자열을 다시 enum 상수로 변환할 수단을 제공해야 할 지 생각해 봐야 한다.

 ```JAVA
private static final Map<String, Operation> stringToEnum = new HashMap<String, Operation>();
static { //상수 이름을 실제 상수로 대응시키는 맵 초기화
  for (Operation op : values()) {
    stringToEnum.put(op.toString(), op);
  }
}
//문자열이 주어지면 그에 대한 Operation 상수 반환.
public static Operation fromString(String symbol) {
  return stringToEnum.get(symbol);
}
```
 - Operation 상수를 stringToEnum 맵에 넣는 것은 상수가 만들어진 다음에 실행되는 static 블록 안에서 한다는 것에 주의하자,,?

### 5. 중첩 enum 자료형(정책 enum 자료형)도 있으니 참고해보자~ PayType 예제염

### 6. 요약.
  - 언제 enum을 써야하나 ? 고정된 상수 집합이 필요할 때 + 원래 열거형인 자료들 포함
  - 가독성도 높고 안전하고 강력하다.
  - 동일한 메서드가 상수별로 다르게 동작하도록 해야하면 switch 대신에 상수별 메서드로 구현하자.
  - 여러 enum 상수가 공통 기능을 이용해야 하는 일이 생길 때는 정책 enum 패턴을 사용해보자~
