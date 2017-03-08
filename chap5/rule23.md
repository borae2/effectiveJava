## 규칙23. 새 코드에는 무인자 제네릭 자료형을 사용하지 마라

- 선언부에 형인자가 포함된 클래스나 인터페이스는 제네릭 클래스나 인터페이스라고 부른다.
- 쉽게 말해서 형인자 : E
- 제네릭 클래스와 인터페이스는 제네릭 자료형이라 부른다.
### 무인자 자료형  : 실 형인자 없이 사용되는 제네릭 자료형
 - List\<E\>의 무인자 자료형은 List이다.
 - 무인자 자료형은 자료형 선언에서 제네릭 자료형 정보가 전부 삭제된 것처럼 동작한다.
 ```JAVA
 //잘못됨
 private final Collection stamps = ...;
 ```

 ```JAVA
 //형인자 컬렉션 자료형 - 형 안전성 확보
 private final Collection<Stamp> stamps = ...;
 ```
 - stamps를 형인자 자료형으로 선언하면, 엉뚱한 자료형의 객체를 넣는 코드를 컴파일 할 때 확인할수있다. (coin넣기)
 - 형인자 자료형을 쓰면 형변환을 하지 않아도 된다.
    + java.util.Date와 java.sql.Date 객체를 혼동해서 넣는 경우가 발생할 수 있다.

### 무인자 자료형을 쓰면 형 안전성이 사라지고, 제네릭의 장점 중 하나인 표현력 측면에서 손해를 보게된다.
 - 나쁜데 지원하는 이유? 호환성 때문.
 - 새로 만드는 코드에서는 List와 같은 무인자 자료형을 쓰면 안 되지만, List\<Object\>는 사용해도 좋다.
 - List는 형 검사를 완전히 생략한 것이고, 반대는 객체를 넣을수 있다는 것을 컴파일러에게 알림.

 ```JAVA
 //실행 도중에 오류를 일으키는 무인자 자료형 사용 예
 public static void main(String[] args) {
   List<String> strings = new ArrayList<String>();
   unsafeAdd(strings, new Integer(42));
   String s = strings.get(0); //컴파일러가 자동으로 형변환
 }
 private static void unsafeAdd(List list, Object o) {
   list.add(o); //무인자 자료형 List이기 때문에 경고가 뜨게됨
 }

 ```
 - ClassCastException 예외 발생.
 - unsafeAdd를 List<Object>로 바꾸면 컴파일 에러가 발생.
 - List\<String\>은 List의 하위 자료형이지만 List\<Object\>의 하위 자료형은 아니다!!!!!!


### 비한정적 와일드카드 자료형
  - 제네릭 자료형을 쓰고 싶으나 실제 형 인자가 무엇인지 모름/신경X 일때는 형 인자로 '?'를 쓰면 된다.
  - Set\<E\>에 대한 "" 는 Set\<?\>

  ```JAVA
  //비한정적 와일드카드 자료형 - 형 안전성과 유연성 만족
  static int numElementsInCommon(Set<?> s1, Set<?> s2) {
    int result = 0;
    for (Object o1 : s1)
      if (s2.contains(o1))
        result ++;
    return result;
  }
  ```
  - 와일드카드 자료형은 안전하지만, 무인자 자료형은 그렇지 않다.
    - 무인자 자료형 컬렉션에는 아무 객체나 넣을 수 있어서, 컬렉션의 불변식이 쉽게 깨진다.
    - 그러나 Collection\<?\>에는 null 이외의 어떤 원소도 넣을 수 없다 - 어떤 자료형의 객체를 담는 컬렉션인지 알 방법이 없기 때문.


### 두 가지 예외
  - 제네릭 자료형 정보가 프로그램이 실행될 때는 지워지기 때문(규칙 25)
  - 클래스 리터럴에는 반드시 무인자 자료형을 써야 한다.
     - 자바 표준에 따르면 클래스 리터럴에는 형인자 자료형을 쓸 수 없다.
     - List.class, String[].class는 가능하지만, List\<?\>.class는 사용할 수 없다.
  - instanceOf 연산자의 경우 (제네릭 정보가 지워지므로) 비한정적 와일드카드 자료형 이외의 형인자 자료형에 적용할 수 없다. / \<?\>를 쓴다고 다르게 동작하지도 않는다.

  ```JAVA
    if (o instanceof Set) { //무인자
      Set<?> m = (Set<?>) o; //와일드~~
    }
  ```

 - o가 확실히 Set 객체이므로, 그뒤엔 와일드~~ 쓰자. (경고 안해염)


### 요약
 - 무인자 자료형 쓰지 마세요. 예외가 생길 수 있어요.
