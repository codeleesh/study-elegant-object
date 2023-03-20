# [엘레강트 오브젝트] 2.5 퍼블릭 상수(Public Constant)를 사용하지 마세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있다.

- 상수
- 잘못된 사용
- 어떻게 해결할까?
- 또 다른 예
- 정리



## 상수



상수를 사용하는 이유는 데이터를 공유하기 위한 목적이다. 그래서 `public static final` 을 사용한다. 

하지만 실제 객체들은 어떤 것도 공유해서는 안되고 독립적으로 닫혀 있어야 한다.



아래 예시를 통해서 알아보도록 하겠다.

```java
class Records {
    private static final String EOL = "\r\n";

    void write(Writer out) {
        out.write(rec.toString());
        out.write(Recoreds.EOL);
    }
}
```

- `EOL` 상수는 private으로 선언되어 있어서 Records 클래스 안에서만 사용되기 때문에 바람직한 상황이다.



## 잘못된 사용



여기서 전혀 다른 Rows 객체가 있다고 가정하겠다.

```java
class Rows {
    private static final String EOL = "\r\n";

    void print(PrintStream pnt) {
        for (Row row : this.fetch()) {
            pnt.print(
                "{ %s }%s", row, Rows.EOL
            );
        }
    }
}
```

- `Records` 와 `Rows` 클래스 모두 `EOL` 이라는 private 상수를 정의하고 있다.
- 이 코드는 중복일 수 있다.



그럼 어떻게 할 수 있을까?

중복이 불편하여서 이러한 중복을 제거하기 위해서 상수를 나타내는 클래스를 생성하고 다음과 같이 작성할 수 있다.

```java
publuc class Constants {
    public static final String EOL = "\r\n";
}
```

- Java에서는 `Constants` 클래스를 통해서 로딩된 모든 클래스들이 `EOL` 이라는 상수에 접근할 수 있다.



코드 중복이라는 문제를 해결하였지만, 2개의 더 큰 문제가 생겨났다.

- 결합도가 높아진 것
- 응집도가 낮아진 것



### 결합도 증가



위 예시를 통해, 두 클래스(`Records`, `Rows`)는 모두 같은 객체에 의존하고 있다. 이 경우에 의존성을 쉽게 분리할 수가 없어졌다.

- `Constants.EOL` 을 변경하는 입장에서는 이 값을 어떤 클래스에서 사용하고 있는지, 어떻게 사용되고 있는지 알 수 있는 방법이 없다.
- `Constants.EOL` 객체는 사용 방법과 관련된 어떤 정보도 제공하지 않은 채 모든 곳에서 접근 가능하다.

많은 객체들이 다른 객체를 사용하는 상황에서 서로를 어떻게 사용하는지 알 수 없다면, 이 객체들은 매우 강하게 결합되어 있는 것이다.



### 응집도 저하



public 상수를 사용하면 객체의 응집도는 낮아진다. 

- 낮은 응집도는 객체가 자신의 문제를 해결하는데 덜 집중한다는 것을 의미



## 어떻게 해결할까?



한 줄을 종료하는 작업을 다른 객체에게 위임한다면 각 객체의 응집도를 향상시킬 수 있다. 객체 사이에 중복하는 것이 아닌 기능을 공유할 수 있도록 새로운 클래스를 만들어야 한다.

```java
class EOLString {
    private final String origin;

    EOLString(String src) {
        this.origin = src;
    }
  
    @Override
    String toString() {
        return String.format("%s\r\n", origin);
    }
}
```

- 한 줄의 마지막에 접미사를 덧붙이는 기능을 `EOLString` 클래스 안으로 완벽하게 고립시켰다.



클래스 사이에 중복 코드를 제거한다면 클래스가 작아질수록 코드는 더 깔끔해진다. 

- 애플리케이션을 구성하는 클래스의 수가 많을수록 설계가 더 좋아지고 유지보수하기도 쉬워진다. 



## 또 다른 예



우리가 자주 사용하는 클래스에서도 이를 적용해볼 수 있다. 모든 HTTP 클라이언트들은 HTTP 요청 메서드를 변경할 수 있는 기능을 제공한다. 

다음과 같이 상수를 통해서 가능하다.



리터럴 문자열 사용

```java
String body = new HttpRequest()
    .method("POST")
    .fetch();
```
상수 사용

```java
String body = new HttpRequest()
    .method(HttpMethod.POST)
    .fetch();
```

하지만, 리터럴을 사용하는 대신 HTTP 메서드를 표현하는 단순한 클래스를 많이 만드는 편이 더 좋다.

```java
String body = new PostRequest(new HttpRequest())
    .fetch();
```

- 새로운 클래스인 `PostRequest` 는 `POST` 리터럴의 의미론인 설정 로직을 내부에 캡슐화한다.
- `POST` 를 기억할 필요가 없어졌으며, 단지 `POST` 방식으로 요청을 전송하기만 하면 된다.



## 정리

- `public` 상수를 이용해서 코드 중복 문제를 해결하지 말고 대신 클래스를 사용해야 한다.