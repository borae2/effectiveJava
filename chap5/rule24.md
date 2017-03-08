## 규칙 24. 무점검 경고를 제거하라

 - 제네릭으로 그냥 갖다쓰면 경고가 무진장 뜬다.

 ``` JAVA
 Set<Lark> exaltation = new HashSet(); //unchecked conversion

 Set<Lark> exaltation = new HashSet<Lark>();
 ```

### 모든 무점검 경고는, 가능하다면 없애야 한다.
 - 형 안전성이 보장되므로 좋다.

### 제거할 수 없는 경고 메세지는, 형 안전성이 확실할 때만 @SupressWarnings("unchecked") 어노테이션을 사용해서 억제하라.
 - 형 안전성 증명도 없이 경고를 억제하면, 프로그램 안전성에 대해 그릇된 생각을 갖게 될 뿐이다.
 - 코드는 아무런 경고 없이 컴파일 되겠지만, 프로그램 실행 도중 ClassCastException이 발생할 가능성은 그대로 남는다.
 - 그냥 냅두면 진짜 경고가 묻혀뿔수도있다.

### @SupressWarnings 어노테이션은 가능한 한 작은 범위에 적용하라.
 - 절대로 클래스 전체에 "" 쓰믄 안된다 / 중요한 경고 메세지를 놓쳐요.
 - ArrayList 클래스 예제중에서, return 부분에 @SupressWarnings 를 쓰고싶을 때, 지역 변수를 선언해서 걔 앞에 붙이면 된다.

### @SupressWarnings 어노테이션을 이용할 때 마다, 그 이유를 주석으로 달아라.
 - 다른 사람이 쉽게 코드를 이해할 수 있다.
 - 달기 힘들면, 니가 잘못 생각했을 수도 있다. 안전하지 못할 수 도 있어.


### 요약
 - 모든 무점검 경고는 프로그램 실행 도중에 ClassCastException이 발생할 가능성을 나타낸다.
 - 열심히 지워봐라.
 
