# [엘레강트 오브젝트] 2.3 항상 인터페이스를 사용하세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

- 객체의 결합
- 객체의 분리
- 연습해보기
- 정리
- 관련해서 참고해볼 내용



## 객체의 결합

- 객체는 다른 객체들과 의사소통하면서 작업을 지원하고, 다른 객체들에게 도움을 제공한다.
  - 결합이 필요한 부분은 다른 누군가가 객체의 서비스를 필요로 하기 때문이다. 

- 이렇듯, 객체들이 서로를 필요로 하기 때문에 결합한다. (`coupled`)
- 객체들의 수가 수십 개를 넘어가면서부터 객체 사이의 강한 결합도가 심각한 문제가 된다.
- 이러한 결합도 문제는 유지보수성에 영향을 미치게 된다.



## 객체의 분리

- 애플리케이션 전체를 유지보수 가능하도록 만들기 위해서는 객체를 분리헤야 한다.(`decouple`)
- 기술적인 관점에서 객체 분리란 상호작용하는 다른 객체를 수정하지 않고도 해당 객체를 수정할 수 있도록 만든다는 것을 의미한다.
- 가장 훌륭한 도구가 바로 `인터페이스` 이다.
  - 인터페이스를 통해서 각각의 경쟁자는 서로 다른 경쟁자를 쉽게 대체할 수 있는데, 이것이 느슨한 결합도(`loose coupling`) 이다.




## 연습해보기

- 클래스 안의 모든 퍼블릭 메서드가 인터페이스를 구현하도록 만들어야 한다.



직원의 월급을 담당하는 객체를 통해서 인터페이스 연습을 해보도록 하겠습니다.

월급을 계산하는 행위를 인터페이스로 나타내도록 하겠습니다.

```java
public interface Cash {

    Cash multiply(final float factor);
}
```

계산하는 행위의 대한 세부 구현을 담당하는 객체는 다음과 같습니다.

```java
public class DefaultCash implements Cash {

    private final int dollars;

    public DefaultCash(final int dlr) {

        this.dollars = dlr;
    }

    @Override
    public Cash multiply(final float factor) {

        return new DefaultCash((int) (this.dollars * factor));
    }

    @Override
    public String toString() {
        return "DefaultCash{" +
                "dollars=" + dollars +
                '}';
    }
}
```

직원의 월급을 계산하는 부분의 대해서 구현 세부사항의 의존하기 보다는 인터페이스의 의존한다면 다음과 같습니다.

```java
public class Employee {

    private Cash salary;

    // 비즈니스 로직
}
```

- 여기서의 핵심은 직원의 월급 계산식은 변경될 수 있기 때문에 확장 포인트를 염두해 두어야 합니다.
- 즉, `Employee` 객체 모르게 변경될 수 있도록 `OCP` 를 지켜야 합니다. 
- 행위를 클래스로 캡슐화해 동적으로 행위를 자유롭게 변경할 수 있습니다.



## 정리

- 클래스 안의 변경 가능한 퍼블릭 메서드가 인터페이스를 구현하도록 만들어야 합니다.



## 관련해서 참고해볼 내용

Spring 환경에서 OCP를 활용한 사례를 참고하고 싶으시면 아래 링크를 확인해보자.

- [[객체지향] OCP 적용 정리](https://lovethefeel.tistory.com/66)
