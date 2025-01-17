# [엘레강트 오브젝트] 1.2 생성자 하나를 주 생성자로 만드세요.

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

다음 목차로 진행합니다.

- 생성자
- 올바른 클래스 설계
- 연습해보기
- 생성자 순서 고려
- 정리



## 생성자

- 새로운 객체에 대한 진입점
- 객체를 생성할 때 호출하는 메서드
- 제공된 인자를 사용해서 캡슐화하고 있는 프로퍼티를 초기화



```java
public class LottoNumber {

    private final int number;

    public LottoNumber(final int number) {
        this.number = number;
    }
}
```

- 생성자 예시



## 올바른 클래스 설계

- 클래스에는 많은 수의 생성자와 적은 수의 메서드를 포함
  - 생성자의 개수가 더 많을수록 클래스는 더 개선되고, 사용자 입장에서 클래스를 더 편하게 사용
- 메서드가 많아지면 클래스의 초점이 흐려지게 된다.
  - 단일 책임 원칙을 위반하지 말아야 한다.
- 생성자가 많아지면 유연성이 향상된다.
  - 주 생성자에서는 초기화 로직을 단 하나의 생성자에만 위치해야 한다.
  - 부 생성자라고 부르는 다른 생성자들이 주 생성자를 호출한다.




### Best Practice

- 클래스 안에는 2 ~ 3개의 메서드와 5 ~ 10개의 생성자를 포함하고 있는 것이 적당하다고 한다.
  - 책에서는 이 값을 뒷받침할 만한 과학적인 근거는 없으며, 임의로 설정하였다.
- 핵심은 적은 수의 메서드와 상대적으로 더 많은 수의 생성자가 존재한다는 점이다.



## 연습해보기

로또 번호를 담당하는 객체를 통해서 예제를 이해해보도록 하자.



### 요구사항

- 로또 번호는 1부터 45까지 번호를 가질 수 있고, 
- 로또 번호는 숫자 또는 문자로 입력받을 수 있다.



### 클래스 생성

로또 번호 6개 중 하나의 번호를 담당하고 있는 특성을 나타내기 위해서 객체의 이름은 다음과 같은 명사로 작성하였다.

- LottoNumber

객체를 생성하였으니 요구 사항의 로직을 구현해보자.

```java
public class LottoNumber {

    private static final int MIN_NUMBER = 1;
    private static final int MAX_NUMBER = 45;
  
    private final int number;

    public LottoNumber(final int number) {
        
        validate(number);
        this.number = number;
    }

    private void validate(final int number) {

        if (number < MIN_NUMBER || number > MAX_NUMBER) {
            throw new IllegalArgumentException("로또 숫자는 1 ~ 45 사이여야 합니다.");
        }
    }
}
```

- 숫자를 입력받는 생성자를 만들었고,
- 생성자 안에서 최소값과 최대값의 검증 로직을 체크한다.

숫자도 입력받고 문자열로된 숫자도 입력받는다면 어떻게 해야 할까?

```java
public LottoNumber(final String number) {
        
	validate(Integer.parseInt(number));
	this.number = number;
}
```

- 물론, 생성자를 하나 더 만들어도 된다.
  - 그렇다면 초기화 로직을 넣어야 하고,
  - 검증 로직도 추가적으로 넣어야 하는데,
  - 이는 곧 중복을 의미하게 된다.

그렇다면, 어떻게 하면 좀 더 간결하게 만들 수 있을까?

- 주 생성자와 부 생성자를 나눈다.
- 부 생성자는 주 생성자를 호출한다.
- 생성자에 검증 로직을 둔다면 중복 코드는 제거되고 좀 더 간결하게 만들 수 있다.

```java
public class LottoNumber {

    private static final int MIN_NUMBER = 1;
    private static final int MAX_NUMBER = 45;
    private final int number;

  	// 주 생성자
    public LottoNumber(final int number) {
        // 검증 로직은 주 생성자로
        validate(number);
        this.number = number;
    }

  	// 부 생성자
    public LottoNumber(final String number) {

        this(Integer.parseInt(number));
    }

  	// 검증 로직
    private void validate(final int number) {

        if (number < MIN_NUMBER || number > MAX_NUMBER) {
            throw new IllegalArgumentException("로또 숫자는 1 ~ 45 사이여야 합니다.");
        }
    }
}
```



## 생성자 순서 고려

- 위와 같이 생성자를 생성하다보면 하나의 주 생성자와 많은 부 생성자를 만들게 된다.
- 주 생성자의 위치를 생성자의 가장 처음 또는 마지막의 위치함으로 팀 내부적으로 생성자의 위치를 정한다면 소스 파악이 규칙적이라서 유지보수 하기에 더 편하게 된다.



## 정리

- 올바른 클래스 설계를 하다보면 메서드보다 상대적으로 많은 수의 생성자를 만들게 된다.
- 생성자가 많아진다면, 주 생성자와 부 생성자를 분리하고 중복 코드를 방지하여 유지보수를 향상시킬 수 있다.





## 관련되서 참고해볼 내용

- 생성자가 너무 많아진다면?
  - 정적 팩토리 메소드를 활용해보자.