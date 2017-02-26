## 규칙18. 추상 클래스 대신 인터페이스를 사용하라

### 1. 추상클래스와 인터페이스의 차이

| 추상클래스                   | 인터페이스                             |
| --------------------------- |:-------------------------------------:|
| 구현된 메서드를 포함          | 포함하지 못함                          |
| 추상 클래스를 반드시 계승     | 모든 메서드 정의 / 일반 규약만 지키면 됨 |

 - 자바는 다중 상속을 허용하지 않기 때문에, 추상 클래스를 사용하게 되면 자료형으로 사용하는 데 많은 제약이 발생하게 된다.


### 2. 이미 있는 클래스를 개조해서 새로운 인터페이스를 구현하도록 하는 것은 간단하다.
 - 필요한 메서드가 다 있는지 확인해서 없으면 추가한 다음 클래스 선언부에 implements 절을 넣으면 된다.
 - 추상 클래스의 경우에는, 자료형 계층의 위쪽에 두어야 하지만, 자료형 계층이 손상된다.

### 3. 인터페이스는 믹스인을 정의하는데 이상적이다.
 - 믹스인(mixin) : 클래스가 **"주 자료형"** 이외에 추가로 구현할 수 있는 자료형으로, 어떤 선택적 기능을 제공한다는 사실을 선언.
 - Comparable를 implements하는 클래스는 다른 객체와의 비교 결과에 따른 순서를 갖는다고 선언된다.

### 4. 인터페이스는 비 계층적인 자료형 프레임워크를 만들 수 있도록 한다.

```JAVA
 public interface Singer {
   AudioClip sing(Song s);
 }
 public interface Songwriter {
   Song compose(boolean hit);
 }

 public interface SingerSongwriter extends Singer, Songwriter {
   AudioClip strum();
   void actSensitive();
 }
```
 - 가수 가운데는 작곡가인 사람도 있기에, 인터페이스 자료형들을 동시에 구현하는 클래스를 만들 수 있으며, 확장한 또다른 인터페이스를 추가할 수도 있다.

### 5. 인터페이스를 사용하면 포장 클래스 숙어(규칙 16)를 통해 안전하면서도 강력한 기능 개선이 가능하다.
 - 추상 클래스를 사용해 자료형을 정의하면 프로그래머는 계승 이외의 수단을 사용할 수 없다 -> 강력하지도 않고, 깨지기도 쉽다.
 - 인터페이스 내에는 메서드 구현을 둘 수 없지만, 프로그래머가 사용할 수 있는 코드를 제공할 방법이 없는 것은 아니다.
 - **추상 골격 구현(abstract skeletal implementation) 클래스를 중요 인터페이스마다 두면, 인터페이스의 장점과 추상 클래스의 장점을 결합할 수 있다.**
   + 인터페이스로는 자료형을 정의하고, 구현하는 일은 골격 구현 클래스에 맡기면 된다.
   + 골격 구현 클래스의 이름은 Abstract*Interface* 와 같이 정한다.

   ```JAVA
    //골격 구현 위에서 만들어진 완전한 List 구현
    static List<Integer> intArrayAsList(final int[] a) {
      if (a == null) {
        throw new NullPointerException();
      }
      return new AbstractList<Integer>() {
        public Integer get(int i) {
          return a[i]; //자동 객체화 (int -> Integer)
        }

        @Override public Integer set(int i, Integer val) {
          int oldVal = a[i];
          a[i] = val; //자동 비객체화 (Integer -> int)
          return oldVal; //자동 객체화(int -> Integer)
        }

        public int size() {
          return a.length;
        }
      };
    }
   ```
   + int값과 Integer 객체의 자동변환 때문에 성능은 좋지 않다.
   + 정적 팩터리 메서드를 통해 반환되는 객체의 클래스가 익명 클래스라는 점에 주의.

 - 추상 클래스를 자료형 정의 수단으로 사용했을 때 만족해야 하는 심각한 제약사항들을 따르지 않아도 추상 클래스를 구현할 수 있도록 돕는것이 골격 구현 클래스의 아름다움이다.
 - 모의 가상 상속 : 골격 구현 클래스를 계승하는 private 내부 클래스(inner class)를 정의하고, 인터페이스 메서드에 대한 호출은 해당 중첩 클래스 객체로 전달. -> 다중 상속의 단점은 피하면서도 장점 대부분은 누릴 수 있다.
