# [엘레강트 오브젝트] 2.7 문서를 작성하는 대신 테스트를 만드세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

- 문서화
- 단위 테스트
- 정리
- 관련해서 참고해볼 내용



## 문서화



문서화는 유지보수에 있어 중요한 구성 요소이다. 

- 구체적으로 문서를 만드는 일이 중요한 것이 아니라 클래스나 메서드에 관한 추가 정보에 얼마나 쉽게 접근할 수 있는 지가 중요하다.
- `코드를 볼 사람이 모든 것을 알고 있다` 라고 생각하며 작성된 코드를 읽는 것은 매우 성가신 일이다.
- 더 읽기 쉬운 코드를 만들기 위해서는, 코드를 읽게 될 사람이 비즈니스 도메인, 프로그래밍 언어, 디자인 패턴, 알고리즘을 거의 이해하지 못하는 주니어 프로그래머라고 가정해야 한다.



## 단위 테스트



나쁜 설계는 문서를 작성하도록 강요한다. 코드 만으로는 이 클래스가 어떤 일을 하는지, 메서드의 목적이 무엇인지, 메서드를 어떻게 사용해야 하는 지를 이해할 수가 없다.



**좋은 클래스**

- 목적이 명확하고, 작고, 설계가 우아하다. 
- 따라서 코드를 문서화하는 대신 코드를 깔끔하게 만들어야 한다. 
- `깔끔하게 만든다` 라는 말에는 단위 테스트도 함께 만든다는 의미가 포함되어 있다. 단위 테스트는 클래스의 일부이지 독립적인 개체가 아니다.



**즉, 단위 테스트가 문서화이다.**

- 깔끔하고 유지보수 가능한 단위 테스트를 추가하면 클래스를 더 깔끔하게 만들 수 있고 유지보수성을 향상 가능하다.
- 더 훌륭한 단위 테스트를 작성할수록 더 적은 문서화가 요구된다.



### 단위 테스트 예시



로또 하나의 번호에 대해서 작성한 단위 테스트 코드를 살펴보면 다음과 같다.

```java
    @DisplayName("로또 숫자는 1 ~ 45 사이 값이 아니면 에러 처리한다.")
    @ParameterizedTest
    @ValueSource(ints = {0, 46})
    void error_create_lotto_number(final int input) {

        assertThatThrownBy(() -> new Number(input))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessage("로또 숫자는 1 ~ 45 사이여야 합니다.");
    }

    @DisplayName("로또 숫자는 1 ~ 45 사이인 경우 생성한다.")
    @ParameterizedTest
    @ValueSource(ints = {1, 45})
    void create_lotto_number(final int input) {

        final Number number = new Number(input);

        assertThat(number.getNumber()).isEqualTo(input);
    }

    @DisplayName("로또 번호는 숫자만 생성가능하다.")
    @ParameterizedTest
    @ValueSource(strings = {"a", "?", "%"})
    void error_bonus_number(final String number) {

        assertThatThrownBy(() -> Number.from(number))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessage("숫자만 입력해야 합니다.");
    }
```

- 해당 내용만 파악하고 로또 하나의 번호가 해야될 일들이 명확하게 느껴진다.
- 굳이 다른 문서가 필요없다.



## 정리



- 더 읽기 쉬운 코드를 만들기 위해서는, 코드를 읽게 될 사람이 비즈니스 도메인, 프로그래밍 언어, 디자인 패턴, 알고리즘을 거의 이해하지 못하는 주니어 프로그래머라고 가정해야 한다.
- 깔끔하고 유지보수 가능한 단위 테스트를 추가하면 클래스를 더 깔끔하게 만들 수 있고 유지보수성을 향상 가능하다.
- 더 훌륭한 단위 테스트를 작성할수록 더 적은 문서화가 요구된다.



## 관련해서 참고해볼 내용



- 문서 자동화란?
  - 문서를 기능과 동기화를 맞추기 어려운데, 이를 해결하기 위해 코드에서 관리
  - 많이 사용하는 도구로는 `Swagger` 와 `Spring Rest Docs` 가 있음
- 문서 자동화 도구 - `Swagger`
  - Swagger는 API Call하여 테스트하는 기능에 특화
  - Swagger는 기능이 많은 반면에 Spring Rest Docs를 단순히 문서임
  - Swagger 사용 시 불필요한 프로덕션 코드 오염이 발생
- 문서 자동화 도구 - `Spring Rest Docs`
  - 테스트 코드에 설정 및 작성하여 프로덕션 코드에 영향이 적음
  - Swagger의 api call 기능은 intellij(local)에서 Http Request라는 기능을 사용하여 대체함