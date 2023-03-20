# [엘레강트 오브젝트] 2.4 메서드 이름을 신중하게 선택하세요

이 내용은 `엘레강트 오브젝트` 를 읽으면서 정리한 내용을 포함하고 있습니다.

- 들어가기
- 빌더는 명사다
- 조정자는 동사다
- 빌더와 조정자 혼합하기
- Boolean 값을 결과로 반환하는 경우
- 정리
- 관련해서 참고해볼 내용



## 들어가기

빌더(`builder`)란 뭔가를 만들고 새로운 객체를 반환하는 메서드를 말한다. Java의 Builder 패턴을 말하는 것은 아니다.

- 빌더 메소드는 항상 뭔가를 반환하고, 이름은 명사로 표현한다.

- 빌더 메소드의 의미를 좀 더 풍부하게 설명하기 위해서 형용사 활용 가능하다.

- 빌더의 예
  
  ```java
  int pow(int base, int power);
  float speed();
  Employee employee(int id);
  String parsedCell(int x, int y);
  ```
  



객체로 추상화한 엔티티를 수정하는 메서드를 조정자(`manipulator`)라고 부른다.

- 조정자의 반환 타입은 항상 void이고, 이름은 항상 동사이다.
- 조정자의 경우 의미를 좀 더 풍부하게 설명하기 위해서 부사 활용 가능하다.
- 조정자의 예
  
  ```java
  void save(String content);
  void put(String key, Float value);
  void remove(Employee emp);
  void quicklyPrint(int id);
  ```



뭔가를 조작한 후 반환하거나, 뭔가를 만드는 동시에 조작하는 메서드가 있어서는 안된다.

```java
int save(String content);               // 저장된 전체 바이트를 반환
boolean put(String key, Float value);   // mpa이 변경된 경우 TRUE를 반환
float speed(float val);                 // speed를 저장한 후 이전 값을 반환
```

- save() 메서드는 조정자이기 때문에 이 메서드들의 이름은 올바르지 않다.
  - save() 메서드는 content를 저장하지만, 빌더처럼 int 값을 반환한다.
  - void를 반환하도록 바꾸거나 byteSaved() 와 같은 이름으로 바꿔야 한다.
- put() 메서드 역시 조정자처럼 동작하지만 빌더처럼 boolean을 반환하기 떄문에 동일한 문제가 발생한다.
  - 아무것도 반환하지 않도록 메서드를 수정하는 방법이 있다.
- speed() 메서드는 값을 저장하는 동시에 이전 값을 반환한다.
  - 나쁜 설계의 또 다른 예라고 할 수 있는데, speed() 메서드는 빌더인 동시에 조정자이기 때문이다.



> get 으로 시작하는 작명 방식에 문제가 있다.
> get 이라는 단어는 동사이지만, getter 는 근본적으로 어떤 값을 반환하는 빌더이다.



## 빌더는 명사다

보통 제과점에서 다음과 같이 주문하지는 않는다.

```
"브라우니를 요리해주세요.", "커피 한 잔 끓여주세요."
```

브라우니를 요리하거나 커피를 끓이는 정확한 방법은 제 관심사가 아니며 제과점에서 신경써야 할 일이며, 그래서 다음과 같이 주문한다.

```
"브라우니 주세요.", "커피 한 잔 주세요."
```



이름 짓기란 주제는 추상적이고 개념적이다.

- 부적절하게 지은 메서드 이름은 객체의 전체적인 개념을 망가트리고 사용자들이 객체를 데이터 집합이나 프로시저들의 모음으로 다루도록 유도한다.
- 객체는 자신의 의무를 수행하는 방법을 알고 있고 존중 받기를 원하는 살아있는 유기체이다.

- 어떤 것을 반환하는 메서드의 이름을 동사로 짓는 것은 잘못된 부분이다.



### 실습해보기

다음 메서드의 이름은 잘못되었다.

```java
InputStream load(URL url);
String read(File file);
int add(int x, int y);
```

수정을 한다면 다음과 같다.

```java
InputStream stream(URL url);
String content(File file);
int sum(int x, int y);
```

- `add` 의 경우 우리는 객체에게 x와 y를 더하라고(add) 요청하지 않는다.
- 대신, 두 수의 합(sum)을 계산하고 새로운 객체를 반환해 달라고 요청한다.



## 조정자는 동사다

객체는 실세계의 엔티티를 대표한다.
- 오직 빌더만이 값을 반환할 수 있고 이름은 명사이다.
- 객체가 뭔가를 조정해야 한다면 이름은 동사이고 반환값이 없다.



### 실습해보기

```java
class Pixel {
    void paint(Color color);
}

// 호출하는 곳
Pixel center = new Pixel(50, 50);
center.paint(new Color("red"));
```

- `paint()` 메서드는 값을 반환하지 않는다.
- 비교를 하자면, 바텐더에게 `음악을 틀어주세요` 라고 요청하는 것과 동일하다.
- 반환타입이 존재한다면 마치 `음악을 틀고 현재의 볼륨 상태를 말해주세요.` 라고 요청하는 것과 동일하다.



객체가 뭔가를 조정해야 한다면 이름은 동사이고 반환값은 없다.



## 빌더와 조정자 혼합하기

하나의 메서드 안에서 너무 복잡한 일을 처리하면 안된다. 이는 곧 SCP를 위반하는 부분이기도 하다.



### 실습해보기

```java
class Document {
    int write(InputStream content);
}
```

- `write()` 메서드는 데이터를 쓰는 동시에, 쓰여진 바이트 수를 카운트 한다.
- 하나의 메서드 안에서 너무 복잡한 일을 처리하고 있다.



다음과 같이 책임을 분리할 수 있다.


```java
class Document {
    OutputPipe output();
}

class OutputPipe {
    void write(InputStream content);
    int bytes();
    long time();
}
```

- `output()` 메서드는 빌더, 이 메서드는 문서에 내용을 쓸 준비를 하는 타입의 갹채를 생성한다.
- `OutputPipe` 인스턴스에 `write()` 를 호출하면 객체는 관련된 데이터를 모으기 시작한다.
- `OutputPipe` 객체를 이용해서 바이트 수와 트랜잭션 처리 시간과 같은 다양한 정보를 수집한다.



## Boolean 값을 결과로 반환하는 경우

가독성 측면에서 이름은 형용사로 지어야 한다. 



### Action

메서드를 읽을 때는 접두사 `is` 를 추가해야 하고, 실제 코드에서는 빼야 한다.



### 실습해보기

문자열이 비어있는지 여부를 반환하는 메소드가 있다고 가정해보겠다.

```java
if (name.emptiness() == true) {
    //
}
```

- 이 문장은 `if emptiness of the name is true` (만약 name의 공백여부가 true라면)
- 일반적으로 `== true` 부분을 생략한다. 메소드의 이름을 형용사로 지어야 더 자연스럽게 읽히는 문장이 된다.

```java
if (name.empty()) {
    //
}
```



## 정리

- 메서드의 목적이 무엇인지 확인해야 한다.
- 빌더인 동시에 조정자여서는 안된다.
- 빌더라면 이름을 명사로, 조정자라면 이름을 동사로 지어야 한다.
- Boolean 값을 반환하는 빌더는 예외에 속하며, 이 경우에는 이름을 형용사로 지어야 한다.



## 관련해서 참고해볼 내용

- [**Command Query Separation 원칙**](https://dundung.tistory.com/183)