# [엘레강트 오브젝트] 2.1 가능하면 적게 캡슐화하세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

다음 목차로 진행합니다.

- 객체의 캡슐화
- 객체의 집합체
- 연습해보기
- 관련해서 참고해볼 내용



## 객체의 캡슐화

- 내부에 캡슐화된 객체 전체를 가리켜 객체의 `상태(state)` 또는 `식별자(identity)`

- 아래는 Cash의 상태 또는 식별자를 나타내는 필드로, Cash의 집합체

    ```java
    class Cash {
        private Integer digits;
        private Integer cents;
        private String currency;
    }
    ```

- 동일한 값의 달러, 센트, 통화를 캡슐화하는 Cash 클래스의 두 객체는 서로 동일합니다.

    ```java
    Cash x = new Cash(29, 95, "USD");
    Cash y = new Cash(29, 95, "USD");
    assert x.equals(y);
    assert x == y;
    ```

    - 위의 두 객체 x와 y의 상태는 동일하지만 식별자는 서로 다르다.
      - `==` 연산자를 사용하면 두 객체가 서로 동일하지 않다는 결과를 얻게 된다.
      - `equals()` 의 기본 구현 역시 두 객체가 서로 동일하지 않다고 판단한다.
      - 그럼, 어떻게 해야 하는지 `연습해보기` 단계에서 확인 가능



객체의 집합체에 대해서 좀 더 자세히 알아보겠다.



## 객체의 집합체

위에서 `Cash` 객체의 집합체는 달러, 통화, 센트를 라는 것을 알 수 있다. 이렇게, 객체는 고수준의 행동을 낳기 위해 함께 동작하는 객체들의 집합체입니다.

  - `책` 은 `페이지, 표지, ISBN` 의 집합체
  - `책장` 은 `책과 제목` 의 집합체
  - `자동차` 는 `바퀴, 엔진, 앞 유리` 의 집합체
  - `차고` 는 `자동차와 주소` 의 집합체
  - `직원` 은 `이름, 나이, 봉급` 의 집합체
  - `회사` 는 `직원, 이름, 관리자` 의 집합체



## 연습해보기

- 복잡성은 직접적으로 유지보수성에 영향을 미치게 한다.
- 객체의 수는 4개 또는 그 이하의 객체를 캡슐화할 것을 권장한다.
- 이유는?
  - 4개 이하의 특성을 통해서 객체를 식별할 수 있기 때문이다.
- 더 많은 객체가 필요하다면 클래스를 더 작은 클래스들로 분해해야 한다.
- Java의 결함을 해결하기 위해 `==` 연산자를 사용하지 말고 항상 `equals()` 메서드를 오버라이드해야 한다.



### 실습

회사의 대한 객체를 통해서 캡슐화해보는 연습을 해보도록 하겠다. 회사는 다음과 같은 정보를 갖고 있다.

```java
public class Company {

    private String name;
    private int age;
    private String sex;
    private String tel1;
    private String tel2;
    private String tel3;
    private String department;
    private String location;
    private boolean admin;
}
```

회사 정보를 다음과 같이 나눠볼 수 있다.
- 직원 정보
- 부서 정보

위 내용을 토대로, 최종 회사가 가지고 있는 정보는 다음과 같습니다.

```java
public class Company {

  	// 직원 정보
    private final Employee name;
		// 부서 정보
  	private final Department department;

    ...
}
```

- 직원 객체는 다음 정보를 포함하고 있다.

    ```java
    public class Employee {

        private final String id;
        private final String name;
        private final int age;
        private final String sex;
        private final Tel tel;
        private final boolean admin;

        ...
    }
    ```

- 직원 정보의 휴대폰 번호도 객체로 관리하면 좋을것 같다.

    ```java
    public class Tel {

        private final String tel1;
        private final String tel2;
        private final String tel3;

        ...
    }
    ```

- 부서 정보도 다음과 같이 저장될 수 있다.
  
    ```java
    public class Department {
    
        private final String deptCd;
        private final String deptName;
    
        ...
    }
    ```



이제 객체 생성 테스트를 해보도로 갛겠다.

- 회사를 생성하는 테스트는 다음과 같습니다.

  ```java
  @DisplayName("생성 테스트")
  @Test
  void create_company_user() {
  
      final Tel tel = new Tel("010", "1111", "1111");
      final Employee employee = new Employee("1", "이상호", 20, "M", tel, false);
      final Department department = new Department("dev", "개발1팀");
      final Company company = new Company(employee, department);
  
      assertThat(company).isEqualTo(new Company(employee, department));
  }
  ```

  - `equals` 는 재정의를 진행
    - 직원의 논리적 동치성을 검사하기 위해서는 직원의 ID 필드만 확인해보면 된다.
      - 그래서,  `equals` 는 재정의를 통해 직원의 ID만 비교하도록 하였다.
    - 부서의 논리적 동치성 또한 부서 코드 필드만 확인해보면 된다.
      - 역시 `equals` 는 재정의를 통해 부서 코드만 비교하도록 하였다.



### 장점

- 많은 정보를 포함하고 있던 회사 객체에 대한 관리 정보가 단순해졌다.
- 단순함은 유지보수 향상으로 이어지게 된다.



## 정리

- 복잡성은 직접적으로 유지보수성에 영향을 미치게 된다.
- 객체의 수는 4개 또는 그 이하의 객체를 캡슐화할 것을 권장한다.
- 더 많은 객체가 필요로 하다면 클래스를 더 작은 클래스로 분해하여햐 한다.



## 관련해서 참고해볼 내용

- 회사와 부서의 ID가 null인 경우는 어떻게 해야 할까?

  ```java
  final Employee 홍길동 = new Employee(null, "홍길동", 20, "M", tel, false);
  
  final Employee 슈퍼맨 = new Employee(null, "슈퍼맨", 20, "M", tel, false);
  
  // 홍길동.equals(슈퍼맨) -> true
  ```

  - 위 경우에는 ID키가 null 이지만 equals 비교 시 true가 된다.

해당 엔티티에는 ID가 null값이 로직상 절대 불가능하지만 해당 생성자는 테스트를 위해서 만들게 될수도 있다.

그럴때는 `ReflectionTestUtils` 활용해볼 수 있다.

- [Guide to ReflectionTestUtils for Unit Testing](https://www.baeldung.com/spring-reflection-test-utils)