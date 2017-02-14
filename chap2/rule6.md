## 규칙6. 유효기간이 지난 객체 참조는 폐기하라

### 1. 메모리 누수 스택 구현 예시

```JAVA
public class Stack {
  private Object[] elements;
  private int size = 0;
 ...

 public void push(Object e) {
   ...
   elements[size++] = e;
 }

 public Object pop() {
   if (size == 0) {
     throw new EmptyStackException();     
   }
   return elements[--size];
 }
 ...
```


- 스택이 커졌다가 줄어들면서 제거한 객체들을 쓰레기 수집기가 처리하지 못함.
- 만기 참조(다시 이용되지 않을 참조)를 제거하지 않는 코드.
- 해당 객체만 쓰레기 수입에서 제외되는 것이 아니라 그 객체를 통해 참조되는 다른 객체들도 수집에서 제외.

```JAVA
public Object pop() {
  if (size == 0) {
    throw new EmptyStackException();
  }
  Object results = elements[--size];
  elements[size] = null; //만기참조제거
  return results;
}
```

### 2. 객체 참조를 null 처리하는 것은 규범이라기 보단 예외적인 조치가 되어야 한다.

- 변수를 정의할 때 유효범위를 최대한 좁게 만든다
  + 처음 사용하는 곳에서 선언 (사용하기 전에 선언하면 어떻게 이용되는지 모름)
  + 거의 모든 지역변수 선언에는 초기값(initializer)이 포함

- 객체에 대한 참조들은 null 처리를 해 주어야 한다.(쓰레기 수집기가 알지 못함)


### 3. 자체적으로 관리하는 메모리가 있는 클래스를 만들 때는 메모리 누수가 발생하지 않도록 주의해야 한다.
- 더이상 참조하지 않는 객체 참조는 반드시 null로 바꿔 주어야 한다.

### 4. 캐시도 메모리 누수가 흔히 발생하는 장소다.
- WeakHashMap으로 캐시를 구현하면, 키에 대한 만기 참조가 되는 순간 삭제가 됨.
- 우리는 arcus를 쓰므로 연동이 가능할지는 고민해 보자.

### 5. 메모리 누수가 흔히 발견되는 또 한 곳은 리스너 등의 역호출자다(callback).

- 역호출자를 명시적으로 제거하지 않으면 남아있으므로, 약한 참조를 저장하는 방법이 좋다.
- 약한 참조 : 객체에 대한 소멸을 막지 않으며, GC의 대상이 된다.

```JAVA
//일반 참조
List<Inner> tmpList = new ArrayList<Inner>();
tmpList.add(new Inner("simple reference is alive."));
System.gc(); //D2에서 하지말라고 함

//약한 참조
List<WeakReference> list = new ArrayList<WeakReference>();
WeakReference<Inner> wr = new WeakReference<Inner>(new Inner("weak reference"));
list.add(wr)
System.gc();
```

참조 : http://dlucky.tistory.com/216
