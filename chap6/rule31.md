## 규칙 31. ordinal 대신 객체 필드를 사용하라

### 1. ordinal()
 - enum상수의 위치를 나타내는 정수값을 반환한다.
```JAVA
//ordinal을 남용한 사례 - 따라하면 곤란쓰
public enum Ensemble {
  SOLO, DUET, TRIO ...;

  public int numberOfMusicians() { return ordinal() + 1 ; }
}
```

 - 상수 순서를 변경하는 순간 위에 메서드가 깨진다. (솔론데 두명이 튀어나옴)
 - 3명인데 다른 3명인 ENUM을 추가해도 절때 3명이 안튀어나옴

### 2. enum 상수에 연계되는 값을 ordinal을 사용해 표현하지 말라. 대신 객체 필드에 저장해야 한다.

```JAVA
public enum Ensemble{
  SOLO(1), DUET(2), OCTET(8), DOUBLE_QUARTET(8) ...;

  private final int numberOfMusicians;
  Ensemble(int size) { this.numberOfMusicians = size; }
  public int numberOfMusicians() { return numberOfMusicians; }
}
```

### 3. ordinal이 있는지 처음 알았는데 대부분의 프로그래머는 이 메서드를 사용할 일이 없을 거라고 하네??
