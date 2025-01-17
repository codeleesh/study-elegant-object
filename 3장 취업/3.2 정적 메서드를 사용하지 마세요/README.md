# [엘레강트 오브젝트] 3.2 정적 메소드를 사용하지 마세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

- 객체 대 컴퓨터 사고
- 선언형 스타일 대 명령형 스타일
- 유틸리티 클래스
- 싱글톤 패턴
- 함수형 프로그래밍
- 조합 가능한 데코레이터
- 정리



정적 메서드를 사용하는 대신 객체를 사용해야 한다. 정적 메소드만 있는 객체는 소프트웨어를 유지보수하기 어렵게 만든다.



## 객체 대 컴퓨터 사고

컴퓨터처럼 생각하기

- 순차적인 사고 방식
- 컴퓨터는 어떤 한 위치에서 명령을 실행하기 시작하고 또 다른 위치에서 실행을 중단한다.
- 명렁의 실행 흐름을 제어할 책임이 우리에게 있다.
  - ex) 정적 메서드




객체지향적으로 생각하기

- CPU에게 할 일을 지시하는 것이 아니라 정의
- 우리는 그저 누가 누구인지만 정의하고 객체들이 필요할 때 스스로 상호작용하도록 제어를 위임한다.
  - ex) 객체를 생성




## 선언형 스타일 대 명령형 스타일

명령형 프로그래밍

- 프로그램의 상태를 변경하는 문장을 사용해서 계산 방식을 서술

```java
public static int between(int l, int r, int x) {
    return Math.min(Math.max(l, x), r);
}
```

- 필요한 그 시점에 즉시 수행되기 때문에 between()은 명령형 스타일의 연산자
- 메서드를 호출한 시점에 CPU가 즉시 결과를 계산



선언형 프로그래밍

- 제어 흐름을 서술하지 않고 계산 로직을 표현

```java
class Between implements Number {
    private final Number num;
    Between(Number left, Number right, Number x) {
        this.num = new Min(new Max(left, x), right);
    }
    @Override
    public int intValue() {
        return this.num.intValue();
    }
}
```

- Between이 무엇인지만 정의하고, 변수 y의 사용자가 intValue()의 값을 계산하는 시점을 결정



### 선언형 방식이 더 좋은 이유

#### 최적화

성능 관점에서 직접 통제할 수 있는 코드가 많을수록 유지보수하기도 쉬워짐

- 명령형 방식

```java
public void doIt() {
    int x = Math.between(5, 9, 13);
    if (/* x가 필요한가? */) {
        System.out.println("x=" + X);
    }
}
```

- 선언형 방식

```java
public void doIt() {
    Integer x =  new Between(5, 9, 13);
    if (/* x가 필요한가? */) {
        System.out.println("x=" + X);
    }
}
```

- CPU에게 결과가 실제로 필요한 시점과 위치를 결정하도록 위임하고, CPU는 요청이 있을 경우에만 계산을 실행



#### 다형성

- 다형성이란, 코드 블록 사이의 의존성을 끊을 수 있는 능력을 말함
- 객체지향 프로그래밍에서 객체는 일급 시민이지만 정적 메서드는 그렇지 않음
- 객체를 다른 객체로부터 완전히 분리하기 위해서는 메서드나 주 생성자 어디에서도 new 연산자를 사용하지 않아야 한다.

```java
Integer x = new Between(
    new IntegerWithMyOwnAlgorithm(5, 9, 13)
);
```



#### 표현력

알고리즘과 실행 대신 객체와 행동의 관점에서 사고하기 시작하면 무엇이 올바른지 느껴지게 될 것

```java
Collection<Integer> evens = new LinkedList<>();
for (int number : number) {
    if (number % 2 == 0) {
        evens.add(number);
    }
}
```

```java
Collection<Integer> evens = new Filtered(
    numbers,
    new Predicate<Integer>() {
        @Override
        public boolean suitable(Integer number) {
            return number % 2 == 0;
        }
    }
)
```



#### 응집도

표현력의 예제서 사용한 코드를 보면, 컬렉션의 계산을 책임지는 모든 코드들은 한 곳에 뭉쳐 있기 때문에, 실수로라도 분리할 수 없다.



## 유틸리티 클래스

유틸리티 클래스란, 실제로는 클래스가 아니라 편의를 위해 다른 메서드들이 사용하는 정적 메서드들을 모아 놓은 정적 메서드들의 컬렉션으로 불린다.
유틸리티 클래스는 어떤 것의 팩토리가 아니기 떄문에 진짜 클래스라고 부를 수 없다.

```java
class Math {
    private Math() {
        // 의도적으로 공백으로 남김
    }
    public static int max(int a, int b) {
        if (a < b) {
            return b;
        }
        return a;
    }
}
```



### 유틸리티 클래스의 문제점

유틸리티 클래스 안에는 객체가 존재하지 않기 때문에 어떤 것도 변경할 수 없습니다. 유틸리티 클래스는 분리할 수 없는 하드코딩된 의존성입니다



## 싱글톤 패턴

싱글톤은 논리적인 관점과 기술적인 관점 모두에서 전역변수 그 이상도 그 이하도 아니다.



### 싱글톤의 문제점

```java
class Math {
    private static Math INSTANCE = new Math();
    private Math() {}
    public static Math getInstance() {
        return Math.INSTANCE;
    }
    public int max(int a, int b) {
        if (a < b) {
            return b;
        }
        return a;
    }
}
```

- Math 클래스는 싱글톤의 대표적인 예
- Math 클래스의 인스턴스는 오직 하나만 존재할 수 있고, 이 유일한 인스턴스의 이름은 INSTANCE
- getInstance()를 호출해서 이 인스턴스에 접근할 수 있음



## 함수형 프로그래밍



FP보다 OOP의 표현력이 더 뛰어나고 강력하다.

- FP에서는 오직 함수만 사용할 수 있지만 OOP에서는 객체와 메서드를 조합할 수 있다.
- 이상적인 OOP 언어에는 클래스와 함께 함수가 포함되어야 한다.
- 작은 프로시저로 동작하는 Java의 메서드가 아니라, 하나의 출구만 포함하는 순수한 FP 패러다임에 기반하는 진정한 함수를 포함해야 한다.



## 조합 가능한 데코레이터



조합 가능한 데코레이터는 그저 다른 객체를 감싸는 객체일 뿐입니다. 다음은 그 예시이다.

```java
names = new Sorted(
    new Unique(
        new Capitalized(
            new Replaced(
                new FileNames(
                    new Directory(
                        "/var/users/*.xml"
                    )
                ),
                "([^.]+)\\.xml",
                "$1"
            )
        )
    )
);
```

- 해당 코드는 매우 깔끔하면서도 객체지향적
- 이 코드는 어떤 일도 실제로 수행하지는 않지만, 다음과 같은 일을 한다.
  - 디렉토리(Directory) 안의 
  - 모든 파일 이름(FileNames)을 
  - 정규 표현식을 이용해서 치환하고(Replaced), 
  - 대문자로 변경하고(Capitalized), 
  - 중복된 이름을 제거해서 유일하게(Unique) 만들고, 
  - 다시 정렬해서(Sorted) 컬렉션에 담은 후, 
  - 이 컬렉션을 가리키는 names 객체를 선언한다.


Directory, FileNames, Replaced, Capitalized, Unique, Sorted 클래스 각각은 하나의 데코레이터입니다. 객체들의 전체적인 행동은 내부에 캡슐화하고 있는 객체들에 의해 유도됩니다.



#### 연산자 if 대신 if 클래스를 제공

```java
float rate;

if (client.age() > 65) {
    rate = 2.5;
} else {
    rate = 3.0;
}
```



#### 객체 지향적인 방식으로 작성한 코드 코드

```java
float rate = new If(
    client.age() > 65,
    2.5, 3.0
);
```



#### 객체 지향적인 방식으로 조금 더 개선한 코드

```java
float rate = new If(
    new Greater(client.age(), 65),
    2.5, 3.0
);
```



#### 최종 코드

```java
float rate = new If(
    new GraterThan(new AgeOf(client), 65),
    2.5, 3.0
);
```

- 이 코드는 순수하게 객체지향적인면서도 선언형
- 어떤 일도 하지 않으며, 오직 rate가 무엇인지만 선언



## 정리

- 올바른 객체지향 설계에서는 정적 메서드를 사용해서는 안된다.
- 객체와 정적 메서드를 혼용해서 사용하지 말고 객체를 사용하도록 코드를 작성한다.
- 클래스가 작업을 수행하는데 필요한 모든 요소들이 생성자에 제공되고 내부에 캡슐화되어야 한다.
- 많은 요소들을 캡슐화할 필요가 있다면, 클래스를 리팩토링 해서 더 작게 만들어야 한다.
- 작으면서도 조합 가능한 클래스들을 설계하고, 더 큰 객체를 조합하기 위해 작은 클래스들을 재사용할 수 있도록 만들어야 한다.
- 객체지향 프로그래밍이란 더 작은 객체들을 기반으로 더 큰 객체들을 조합하는 작업이다.
- 소프트웨어 어디에서도 static 키워드를 사용해서는 안된다. 이것이 미래에 코드를 재사용할 누군가를 위하는 길이다.



## 관련해서 생각해볼 내용



생성자와 정적 팩토리 메서드를 통해 얻고자 하는 것은 다음과 같다.

- 인스턴스 객체

정적 팩토리 메소드를 사용하는 이유는 무엇일까? 가장 큰 장점은 다음과 같다.

- 이름을 지을 수 있어서 사용자로 하여금 유의미한 정보를 전달 가능

만일, 생성자와 정적 팩토리 두 메서드가 해당 객체를 생성하기 위해서 동일한 파라미터를 받는다면 정적 팩토리 메소드는 필요할까?

- 생성자로도 그 역할을 충분히 할 수 있다.

- 정적 팩토리 메소드를 사용하는 장점이 부각되지 않는다.



그래서 기준을 세워보면 좋다. 

생성자의 경우에는 다음과 같다.

-  인스턴스가 가지는 필드를 추가적인 가공없이, 필드와 동일한 타입의 파라미터를 받아서 생성하는 경우

정적 팩토리 메소드의 경우에는 다음과 같다.

- 특정한 로직을 통해 객체를 생성
- 클래스의 필드와 다른 타입을 통해 객체를 생성

클래스가 가지고 있는 필드와 다른 타입으로 객체를 생성하려고 한다면 분명 특정한 로직을 통해 타입 변경이 필요할 것이고, 이는 결국 첫번째 기준인 `로직을 통해 객체를 생성` 하는 부분이 되므로 결국 두 가지 내용은 동일하다.



다음은 예시 코드이다.

```java
public class Names {
    private final List<String> names;

    public Names(final List<String> names) {
        this.names = names;
    }

    public static Names from(final String nameInput) {
        List<String> names = Array.stream(nameInput.split(","))
                .map(String::trim)
                .collect(Collectors.toList());

        return new Names(names);
    }
}
```



명확한 기준을 통해서 생성자와 정적 팩토리 메서드를 구분지어 사용한다면 다음과 같다.

- 생성자의 경우, 해당 코드를 보는 사용자는 어떠한 객체를 마주하더라도 (생성자를 통해) 생성할 때는 `아무런 가공없이 전달하는 값으로 객체 생성` 한다는 것을 인지할 수 있다.
- 정적 팩토리 메서드의 경우,  `직접 가공을 해서 객체를 생성해야 하는 불편함을 정적 팩토리 메서드에 전달해주면 특정한 로직을 통해 객체를 생성` 한다는 것을 인지할 수 있다.

지금과 같은 관점으로 코드 전체적인 부분에서 일관되게 가져가면서 코드를 작성한다면 더 좋은 코드를 작성할 수 있게 된다.