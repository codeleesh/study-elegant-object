# [엘레강트 오브젝트] 1.1 -er로 끝나는 이름을 사용하지 마세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

다음 목차로 진행합니다.

- 클래스
- 객체를 바라봐야 하는 입장
- 접미사 -er
- 클래스 이름을 짓는 적절한 방법
- 정리



## 클래스

- 객체의 팩토리
  - 객체를 만들고, 추적하고, 적절한 시점에 파괴
  - 객체를 인스턴스화한다.
- new 연산자가 충분히 강력하지 않는 이유 
  - 유사한 객체가 존재하거나 재사용 가능한 지를 확인하지 않는다.



## 객체를 바라봐야 하는 입장

- 클래스는 객체를 꺼내거나 반환할 수 있는 위치이기 때문에, 클래스를 저장소 또는 웨어하우스라고 불러야 한다는 점
- `도메인 주도 설계` 의 입장으로
  - 도메인이 비즈니스 로직의 주도권을 가지고 개발하는 것
  - 서비스의 많은 로직이 엔티티로 이동
  - 서비스는 엔티티를 호출하는 정도의 얇은 비즈니스 로직 
  

정리해보면, 객체는 어플리케이션의 기능을 구현하기 위해 존재하며, 기능 구현의 핵심 비즈니스 로직의 주도권을 객체가 가지고 개발해야 한다.
객체를 단순히 자료구조 용으로 사용하면 안된다. `feat) setter, getter`

> 객체를 살아있는 생명체로 생각해야 합니다.



## 접미사 -er

- `-er` 로 끝나는 객체가 주는 의미
  - `-er` 로 끝나는 이름의 클래스들
    - Manager, Controller, Helper, Handler, Writer, Reader, Converter, Validator, Router, Dispatcher, Observer, Listener, Sorter, Encoder, Decoder
  - `-er` 로 끝나는 클래스는 객체가 아니라 어떤 데이터를 다루는 절차들의 집합으로 느껴짐

> 클래스의 객체들이 무엇을 캡슐화할 것인지를 관찰하고 이 요소들에 붙일 적합한 이름을 찾아야 합니다.



## 클래스 이름을 짓는 적절한 방법

- 잘못된 방법
  - 클래스의 객체들이 무엇을 하고 있는(doing)지를 살펴본 후 기능(functionality)에 기반해서 이름을 작성
  - 클래스의 이름은 객체가 노출하고 있는 기능에 기반
    ```java
    class CashFormatter {
    	private int dollars;
    	CashFormat(int dlr) {
    		this.dollars = dlr;
    	}
    	public String format() {
    		return String.format("$ %d", this.dollars);
    	}
    }
    ```
    
  - 해당 메소드를 실제로 사용한다면 다음과 같은 모습
    ```java
    final CashFormatter cashFormatter = new CashFormatter(1_000);
    final String usd = cashFormatter.format();
    ```
    - 객체의 역할이 잘 들어난다고 생각이 들지 않는다.
      - `format` 의 중복이 느껴지며, `format` 메소드의 역할이 나타나지 않는다.

- 올바른 방법
  - 클래스의 이름은 무엇을 하는지(what he does)가 아니라 무엇인지(what he is)에 기반해서 이름을 작성

    ```java
    class Cash {
    	private int dollars;
    	Cash(int dlr) {
    		this.dollars = dlr;
    	}
    	public String usd() {
    		return String.format("$ %d", this.dollars);
    	}
    }
    ```

  - 해당 메소드를 실제로 사용한다면 다음과 같은 모습

    ```java
    final Cash cash = new Cash(1_000);
    final String usd = cash.usd();
    ```
    - 객체의 역할이 잘 들어난다.
      - 현금의 `USD` 변환의 특징이 잘 나타난다.



### 연습해보자

임의의 숫자 리스트 중 소수를 담당하는 객체의 이름을 만들어보도록 해보면,

- 어떤 것이 적합한 이름인가? 
  - Primer, PrimeFinder, PrimeChooser, PrimeHelper, PrimeNumbers
- 소수만으로 구성된 특징이 잘 나타나도록 -> `PrimeNumbers` 

> 클래스의 객체들이 무엇을 캡슐화할 것인지를 관찰하고 이 요소들에 붙일 적합한 이름을 찾아야 합니다.



## 정리

- 객체는 어플리케이션의 기능을 구현하기 위해 존재하며, 기능 구현의 핵심 비즈니스 로직의 주도권을 갖는다.
- 새로운 클래스에 이름을 붙일 때는 **무엇을 하는지(what he does)**가 아니라 무엇인지(what he is)를 생각해야 한다는 것



## 관련되서 참고해볼 내용

- 디자인 패턴 중 `Information Expert`
  - 책임을 수행할 수 있는 데이터를 가지고 있는 객체에 책임을 부여 
  - 객체는 데이터와 비즈니스 로직이 함께 존재
  - 정보 은닉을 통해 자신의 데이터를 감추고 오직 Public Method로만 데이터를 처리하고, 외부에는 그 기능(책임)만을 제공

- 우리가 사용하고 있는 Spring의 `@Service` 설계 사상 `Domain-Driven Design`

- 아래는 `@Service` 의 Java Doc

  ```java
  @Target(value=TYPE)
  @Retention(value=RUNTIME)
  @Documented
  @Component
  public @interface Service
  Indicates that an annotated class is a "Service", originally defined by Domain-Driven Design (Evans, 2003) as "an operation offered as an interface that stands alone in the model, with no encapsulated state."
  May also indicate that a class is a "Business Service Facade" (in the Core J2EE patterns sense), or something similar. This annotation is a general-purpose stereotype and individual teams may narrow their semantics and use as appropriate.
    
  This annotation serves as a specialization of @Component, allowing for implementation classes to be autodetected through classpath scanning.
  ```
