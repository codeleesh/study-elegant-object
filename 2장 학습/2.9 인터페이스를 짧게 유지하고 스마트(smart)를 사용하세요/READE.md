# [엘레강트 오브젝트] 2.9 인터페이스를 짧게 유지하고 스마트(smart)를 사용하세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

- 스마트 클래스
- 데코레이터 활용
- 정리



## 스마트 클래스

클래스를 작게 유지하는 것이 중요하다면, 인터페이스를 작게 만드는 것은 훨씬 더 중요하다.

```java
interface Exchange {
    float rate(final String target);
    float rate(final String source, final String target);
}
```

- 같은 기능을 동작하는 2개의 인터페이스 메소드는 구현 클래스에게 너무 많은 것을 요구하고 있다.
- 이런 종류의 계약은 단일 책임 원칙(Single Responsibility Principle)을 위반하는 클래스를 만들도록 유도한다.
- 두 rate 메서드는 매우 밀접하게 연관되어 있지만 두 개의 독립적인 함수이다.
- 하나의 인자를 받는 rate() 메서드는 이 인터페이스에 포함되어서는 안된다.



이를 해결하기 위해서 스마트 클래스를 사용할 수 있다.

```java
interface Exchange {
    float rate(final String source, final String target);
    final class Smart {
        private final Exchange origin;
        public float toUsd(final String source) {
            return this.origin.rate(source, "USD");
        }
    }
}
```

- 이 스마트 클래스는 아주 명확하고 공통적인 작업을 수행하는 많은 메서드들을 포함할 수 있다.
- 스마트 클래스를 통해서 인터페이스를 구현하는 서로 다른 클래스 안에 동일한 기능을 반복해서 구현하고 싶지 않기 때문이다.



다음과 같이 사용해볼 수 있다.

```java
final float rate = new Exchange.Smart(new NYSE()).toUsd("EUR");
```

- 하지만 위와 같은 코드를 사용하는 곳이 점점 많이진다면 `EUR` 코드를 계속 작성해야 한다.
- 이 때 새로운 기능을 추가하려고 할때 Smart 클래스를 이용해서 추가할 수 있다.



```java
interface Exchange {
    float rate(final String source, final String target);
    final class Smart {
        private final Exchange origin;
        public float toUsd(final String source) {
            return this.origin.rate(source, "USD");
        }
				public float eurToUsd() {
          return this.toUsd("EUR");
        }
    }
}

// use
final float rate = new Exchange.Smart(new NYSE()).eurToUsd();
```

- 이처럼 많은 기능을 제공하려면 Smart 클래스의 크기는 점점 커지게 된다. 
- 하지만 interface는 적은 메서드만을 제공할 수 있다. 
- 기본적으로 interface는 짧게 만들고 Smart 클래스를 인터페이스와 함께 배포함으로써 공통 기능을 추출하고 코드 중복을 피할 수 있다.



## 데코레이터 활용

스마트 클래스는 객체에 새로운 메서드를 추가하지만 데코레이터는 이미 존재하는 메서드를 오버라이드하여 유연하게 사용할 수 있다.

```java
interface Exchange {
    float rate(final String source, final String target);
    final class Fast implements Exchage {
      	private final Exchange origin;
      	@Override
      	public float rate(final String source, final String target) {
          	final float rate;
            if (source.equals(target)) {
                rate = 1.0f;
            } else {
                rate = this.origin.rate(source, target);
            }
            return rate;
        }
        public float toUsd(final String source) {
            return this.origin.rate(source, "USD");
        }
    }
}
```




## 정리

- 기본적으로 인터페이스를 짧게 만들고 스마트 클래스를 인터페이스와 함께 배포함으로써 공통 기능을 추출하고 코드 중복을 피할 수 있다.

