# 7.1.1 가변 클래스는 오용하기 쉽다

클래스를 가변으로 만드는 가장 쉬운 방법은 setter를 사용하는 것이다.

다음은 setter를 사용하는 책의 예시이다.

<img width="664" alt="가변 클래스" src="https://user-images.githubusercontent.com/95729738/225170781-c22b4a9d-8230-42a8-bab7-60a74e16dd3b.png">

폰트와 폰트 크기는 `setFont()`, `setFontSize()` 를 통해서 언제 어디서든 변경할 수 있다.
```java
class UserDisplay {
    
    private final MessageBox messageBox;

    void sayHello() {
            new TextOptions(Font.ARIAL, 12.0); // 폰트와 폰트 크기 12로 설정
            ...
    }

    void renderTitle(...) {
            baseStyle.setFontSize(18.0); // 생성된 폰트 크기인 12.0 에서 18.0으로 변경
    }
}

# 7.1.2 해결책 : 객체를 생성할 때만 값을 할당하라

모든 값이 객체의 생성 시에 제공되고 그 후는 변경할 수 없도록 하면서

클래스를 불변으로 만들 수 있고 오용을 방지할 수 있다.

<br>

클래스 내에서 변수를 정의할 때 클래스 내에서도 변수의 값이 변경되지 않도록 할 수 있다.

자바에서는 `final` 키워드를 사용하여 클래스 내에서도 변수의 값이 변경되지 않도록 할 수 있다.

`final` 키워드를 붙이면 해당 변수를 변경하는 코드를 실수로 추가하는 것을 방지하고,

그 변수들은 절대 변경되지 않을 것이고, 변경되어서도 안 된다는 점을 명시한다.

```java
class TextOptions {

    // 멤버 변수는 final로 선언한다.
    private final Font font;
    private final Double fontSize;

    // 멤버 변수는 인스턴스 생성 시에만 설정된다.
    TextOptions(Font font, Double fontSize) {
            this.font = font;
            this.fontSize = fontSize;
    }

    ...
} 
``` 
이제 TextOptions 객체를 변경해서 오용할 수 없게 되었다.

하지만 외부에서 TextOptions의 폰트 사이즈를 변경해야하는 상황이 오게 되면 어떻게 해야할까?

이러한 경우에 **쓰기 시 복사 패턴**(copy-on-write)을 사용할 수 있다.

이와 관련한 해결책에 대해서는 다음 절에서 다룬다.

# 7.1.3 해결책 : 불변성에 대한 디자인 패턴을 사용하라

불변 객체를 만들었지만, 값을 변경해서 사용해야 할 경우들이 있다.

이를 위해서 2가지 유용한 디자인 패턴을 적용할 수 있다.

```java
1. 빌더 패턴
2. 쓰기 시 복사 패턴
```

---

## 1. 빌더 패턴
빌더 패턴은 **일부 값은 필수, 일부 값은 선택 사항**일 때 사용하면 유용하다.

앞의 예시에서 글꼴은 필수 값, 글꼴 크기는 선택적 값이라고 생각해보자.

아래 그림은 빌더 패턴의 적용 예시이다.
<img width="614" alt="빌더 패턴" src="https://user-images.githubusercontent.com/95729738/225171639-0d1419d7-afdb-4a1c-8253-4299e5f7fd66.png">

`TextOptionsBuilder` 클래스가 `TextOptions` 에 대한 빌더 클래스이다.

`TextOptionsBuilder` 클래스를 살펴보면, 다음과 같은 과정을 거친다.

```java
1. 생성자를 통해 필수 값을 받아 필수 값을 저장한다.
2. 선택 값들은 setXxx 메소드를 통해 설정한다.
    이때, 메소드 체이닝을 이용하여 build 할 수 있도록 return this로 자기 자신을 반환한다.
3. 1, 2번 과정을 거쳐 값을 결정하면 build()를 호출하여 지정한 값으로 생성된 클래스를 반환한다.
```

다음 그림은 `TextOptionsBuilder` 클래스와 `TextOptions` 의 관계를 보여준다.

<img width="643" alt="빌더 패턴 클래스 관계" src="https://user-images.githubusercontent.com/95729738/225171728-09218d05-ef63-406b-aba2-60561c424f2e.png">

외부에서 빌더 패턴을 사용할 때는 다음과 같이 사용한다.

### 1. 필수 글꼴 값 + 선택 글꼴 크기 값 모두 지정
```java
TextOptions getDefaultTextOptions() {
    return new TextOptionsBuilder(Font.ARIAL)
                .setFontSize(12.0)	
                .build();	
}
```

### 2. 필수 글꼴 값만 지정
```java
TextOptions getDefaultTextOptions() {
    return new TextOptionsBuilder(Font.ARIAL)	
                .build();	
}
```
이렇듯, 빌더 패턴은 **값의 일부 or 전체가 선택 값일 때 불변 객체를 만드는 유용한 방법이다.**

그러나 생성 후에 클래스의 인스턴스 복사본을 수정해야 하는 경우

빌더 패턴을 이용할 수 있지만 이 작업은 번거로울 수 있다.

따라서 이 경우에 **쓰기 시 복사 패턴**을 사용할 수 있다.

---

## 2. 쓰기 시 복사 패턴

빌더 패턴을 사용하면 클래스 생성 시에 불변 객체를 만들 수 있다.

<br>

하지만, 클래스의 인스턴스를 변경해야 하는 경우도 있다.

앞선 예시처럼 생성 후에, 글꼴 크기가 변경되어야 하는 상황을 예로 들 수 있다.

<br>

일반적인 상황에서는 setter를 사용하면 간단하게 해결할 수 있지만

앞에서도 말했듯이 큰 문제들이 발생할 수 있어서 지양하는 것이 좋다.

<br>

이때 **쓰기 시 복사 패턴**을 사용하면 해결할 수 있다.

책의 예시에 적용하면 다음과 같다.

<img width="542" alt="쓰기 시 복사 패턴" src="https://user-images.githubusercontent.com/95729738/225172042-d1f5e3cd-b684-40c5-b8a4-cf0003a1c8aa.png">

`withFont()` , `withFontSize()` 처럼 반환 시에 내부 필드를 반환하는 것이 아니라,

바꿀 변수 값을 지정해서 생성한 새로운 `TextOptions` 인스턴스를 반환하는 것이다.

다음 그림은 쓰기 시 복사 패턴이 `TextOptions` 클래스에서 어떻게 작동하는지 보여준다.

<img width="655" alt="쓰기 시 복사 패턴 작동 원리" src="https://user-images.githubusercontent.com/95729738/225172166-05479b90-75de-40ca-80dc-ff4d13e4ffc7.png">

외부에서 쓰기 시 복사 패턴을 사용할 때 다음과 같이 사용할 수 있다.

```java
TextOptions getDefaultTextOptions() {
    TextOptions textOptions = new TextOptions(Font.ARIAL);
    return textOptions.withFontSize(12.0);
}
```

이렇게 사용하면, 처음에 글꼴만 가지고 생성했던 `textOptions` 인스턴스의 내부는 변하지 않고,

해당 인스턴스의 값을 이용해서 폰트 크기를 12로 지정한 새로운 인스턴스를 최종으로 반환해서 사용하게된다.

