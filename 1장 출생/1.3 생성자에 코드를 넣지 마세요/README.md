# [엘레강트 오브젝트] 1.3 생성자에 코드를 넣지 마세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

다음 목차로 진행합니다.

- 생성자의 역할
- 생성자의 코드가 없어야 하는 이유



## 생성자의 역할

- 생성자는 객체의 초기화를 시작하는 장소
- 초기화 시 생성자안에는 `코드가 없어야` 하고 인자를 건드려서는 안된다.
- 코드가 필요하다면 인자들을 다른 타입의 객체로 감싸거나 가공하지 않은 형식으로 캡슐화한다.
- 생성자는 코드가 없어야하고 오직 할당문만 포함해야 한다.



## 생성자의 코드가 없어야 하는 이유

문자열을 정수로 파싱하는 클래스가 있다고 가정하고 예시를 통해 알아보자.

- 아래 코드는 `intValue()` 를 호출할 때마다 매번 텍스트를 정수로 파싱한다.

```java
class StringAsInteger implements Number {

    private String text;
  
    public StringAsInteger(String txt) {
        this.text = txt;
    }
  
    public int intValue() {
        return Integer.parseInt(this.text);
    }
}
```

- 아래 코드는 생성자에서 초기화하는 시점에 텍스트를 정수로 파싱을 단 한번 수행한다.
  
```java
class StringAsInteger implements Number {
  
    private String number;
  
    public StringAsInteger(String txt) {
        this.number = Integer.parseInt(txt);
    }
  
    public int intValue() {
        return this.num;
    }
}
```

생성자에서 직접 파싱을 수행하는 경우 실행 여부를 제어할 수 없다.

- 파싱을 수행하지 않도록 막을 수 있는 방법이 없기 때문에 객체를 생성할 때마다 전달된 인자를 즉시 처리해야 한다.
- 캡슐화하고 `나중에 요청이 있을 때` 파싱하도록 하면, 클래스의 사용자들이 파싱 시점을 자유롭게 결정할 수 있게 된다.



즉, 생성자에 코드가 없을 경우 성능 최적화가 더 쉽다. 생성자에서 다른 일을 수행하지 않는 편(`코드가 없는 경우`)이 좋은 이유는 무엇일까?

- 일관성이라는 측면에서 생각해야 한다.
- 해당 클래스에 미래에 어떤 일이 발생하지 모른다.
- 생성자 안에서 어떤 일을 처리하고 있다면 나중에 리팩토링하기 훨씬 어렵다.



파싱이 여러 번 수행되지 않도록 하고 싶다면 `데코레이터` 를 추가해서 최초의 파싱 결과를 캐싱할 수 있다. (아래는 예시)

- `데코레이터` 패턴 생성자 예시
```java
class CachedNumber implements Number {
  
    private Number origin;
    private Collection<Integer> cached = new ArrayList<>(1);
  
    public CachedNumber(Number num) {
        this.origin = num;
    }
  
    public int intValue() {
        if (this.cached.isEmpty()) {
            this.cached.add(this.origin.intValue());
        }
        return this.cached.get(0);
    }
}
```

- 실행 예시

```java
Number num = new CachedNumber(new StringAsInteger("123"));
num.intValue();  // 첫 번째 파싱
num.intValue();  // 여기에서는 파싱하지 않음
```



## 정리

- 생성자는 객체를 만드는 일 이외에는 어떤 일도 수행하지 않는다.
- 가벼운 생성자는 설정하기 쉽고 투명하게 사용할 수 있기 때문에 객체를 더 빠르게 만들 수 있다.
- 생성자의 코드가 없기 때문에 검증 또는 파싱 등 로직에 대해서 일관되게 처리할 수 있다.