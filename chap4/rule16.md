## 규칙 16. 계승하는 대신 구성하라
 - 계승은 코드 재사용을 돕는 강력한 도구지만, 항상 최선이라고는 할 수 없다.
 - 상위, 하위 클래스를 같은 프로그래머가 통제하는 단일 패키지 안에서 사용하면 안전하다.
 - 이번 규칙은 interface를 implements 및 extends 경우가 아닌, 클래스를 extends 하는 경우를 살펴본다.

### 1. 메서드 호출과 달리, 계승은 캡슐화 원칙을 위반한다.
 - 상위 클래스의 구현은 릴리스가 거듭되면서 바뀔 수있는데, 그러면 하위 클래스 역시 망가질 수 있다.
 - 상위 클래스 작성자가 계승을 고려해 클래스를 설계하고 문서를 만들지 않으면, 하위 클래스는 계속해서 변화해야한다.
 - 예제)HashSet의 add 및 addAll을 재정의 함 -> 재정의한 addAll() 호출 -> super.addAll 호출 -> add를 또 호출함(3이 아니라 6)
   + 하위 클래스에서 재정의한 addAll 메서드를 삭제하면 이 문제를 교정할 수 있지만, HashSet은 add 위에 addAll이 구현되었으므로 가능함
   + 자기 메서드를 호출할 지 모르는 상위 클래스 메서드를 수정하는 것은 어렵고, 시간도 많이 걸리고, 오류도 나기 쉬운 작업이다.

 - 기존 메서드를 재정의하는 대신 새 메서드를 넣으면 안전하나, 재수없게도 상위 클래스에서 같은 메서드 시그니쳐인데 반환값 자료형이 다를 경우, 컴파일 에러가 날 것이다.

### 2. 기존 클래스가 새 클래스의 일부가 되도록 구성하라.
 - 새로운 클래스에 기존 클래스 객체를 참조하는 private 필드를 하나 둔다.
 - 전달 : 새로운 클래스에 포함된 각각의 메서드는 기존 클래스에 있는 메서드 가운데 필요한 것을 호출해서 그 결과를 반환.
 - 기존 클래스의 구현 세부사항에 종속되지 않아 견고하다.

```JAVA
// 계승 대신 구성을 사용하는 포장(wrapper) 클래스
class InstrumentedSet<E> extends ForwardingSet<E>{
  // Set 객체를 감싸고 있다.

 private int addCount = 0;

 public InstrumentedSet(Set<E> s) {
   super(s);
 }

 @Override
 public boolean add(E e){
   addCount++;
   return super.add(e);
 }

 @Override
 public boolean addAll(Collection<? extends E> c){
   addCount += c.size();
   return super.addAll(c);
 }

 public int getAddCount(){
   return addCount;
 }
}

// 재사용 가능한 전달(forwarding)(위임) 클래스
class ForwardingSet<E> implements Set<E> {

 // Set 객체
 private final Set<E> s;

 public ForwardingSet(Set<E> s){
   this.s = s;
 }

 @Override
 public int size() { return s.size(); }
 @Override
 public boolean isEmpty() { return s.isEmpty();}
 @Override
 public boolean contains(Object o) { return s.contains(o);}
 @Override
 public Iterator<E> iterator() {return s.iterator();}
}
```
 - InstrumentedSet 클래스는 Set 인터페이스를 구현하며, Set 객체를 인자로 받는 생성자를 하나 갖고 있다.
   + 결국 이 클래스는 Set 객체를 인자로 받아, 필요한 기능을 갖춘 다른 Set 객체로 변환하는 구실을 한다.
 - 계승을 이용한 접근법은 한 클래스에만 적용이 가능하고, 상위 클래스 생성자마다 별도의 생성자를 구현해야 한다.
   + 하지만 포장 클래스(wrapper class) 기법을 쓰면 어떤 Set 구현도 원하는 대로 수정할 수 있고, 이미 있는 생성자도 그대로 쓸 수 있다.
```JAVA
Set<Date> s =new InstrumentedSet<Date>(new TreeSet<Date>(cmp));
Set<E> s2 = new InstrumentedSet<E>(new HashSet<E>(capacity));
```
 - 이런 패턴을 장식자 패턴이라고 부르는데, 기존 Set 객체에 기능을 덧붙여 장식하는 구실을 하기 때문.
 - 위임이라고 부르기도 한다.


 - 단점 :
   + 역호출 프레임워크와 함께 사용하기에는 적합하지 않다.
   + 포장된 객체는 포장 객체에 대해서는 모르기 때문에 자신에 대한 참조를 넘김(this)
   + 따라서 역 호출 과정에서 포장 객체는 제외된다.


### 3. 요약

1. 계승은 상하위 클래스간 IS-A 관계가 성립할 때만 계승.

2. 그렇다 하더라도 계승은 문제를 일으킬 수 있으므로 구성과 전달 기법을 사용하라

3. 포장 클래스 구현에 적당한 인터페이스가 있다면 더더욱!
