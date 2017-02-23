## 규칙 17. 계승을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 계승을 금지하라.

### 1. 재정의 가능 메서드를 내부적으로 어떻게 사용하는지 반드시 문서에 남기라.
 - public, protected로 선언된 모든 메서드와 생성자에 대해, 어떤 재정의 가능 메서드를 어떤 순서로 호출하는지, 결과가 어떤 영향을 끼치는지 문서로 남기라.
 - 재정의 가능하다는것은 비-final / public / protected 메서드이다.
 - 일반적으로 메서드 주석문 마지막에, "이 구현은" 이라는 문구로 어떻게 호출하는지 나타낸다.

### 2. 클래스 내부 동작에 개입할 수 있는 훅(hooks)을 신중하게 고른 protected 메서드 형태로 제공해야한다.
 - 계승을 고려하여 클래스를 설계할 때 protected로 선언한 멤버를 정하는 방법? 없다. 실제로 하위 클래스를 만들어 보면서 테스트 하라.

### 3. 계승을 위해 설계한 클래스를 테스트할 유일한 방법은 하위 클래스를 직접 만들어 보는 것이다.
 - 중요한 멤버를 protected로 선언.
 - 사용할 일이 없는 멤버는 private으로.

### 4. 계승을 허용하려면 반드시 따라야 할 제약사항이 있다.
 - 첫째, 생성자는 직/간접적이건 재정의 가능 메서드를 호출해서는 안 된다.


### 5. 계승에 맞도록 설계하고 문서화하지 않은 클래스에 대한 하위 클래스는 만들지 않는 것이다.

### 6. 오늘의 발표자 : 장성호