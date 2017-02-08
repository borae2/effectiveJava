## 규칙4. 객체 생성을 막을 때는 private 생성자를 사용하라


###객체 생성을 막을 필요가 있는 경우
- 특정 인터페이스를 구현하는 객체를 만드는 팩터리 메서드 등의 정적 메서드
- final 클래스에 적용할 메서드
- Utility 클래스


### 1. 객체를 만들 수 없도록 하려고 클래스를 abstract로 선언 해 봤자 소용없다. <br/> -> private 생성자로 해결

```JAVA
public class UtilityClass {
  //기본 생성자가 자동 생성되지 못하도록 하여 객체 생성 방지
  private UtilityClass() {
    throw new AssertionError();
  }
}
```

- 명시적으로 정의된 생성자가 private 이므로, 클래스 외부에서는 사용할 수 없다.
- 하위 클래스를 만들 수 없음. (호출 가능한 생성자가 없기 때문)

