# **6. 예측 가능한 코드를 작성하라**

예측 가능한 코드를 작성하는 것은 **무언가를 분명하게 하는 것**일 때가 많습니다. 함수가 아무것도 반환하지 않을 때가 있거나 처리해야 할 특별한 시나리오가 있는 경우 이 사실을 **다른 개발자에게 확실하게 알려야 합니다.** 그렇지 않으면 코드의 동작이 그들이 예상하는 것과 일치하지 않을 위험이 있습니다. 따라서 이번 장에서는 코드가 예측을 벗어나는 몇 가지 경우와 이를 피하기 위한 기술을 살펴보겠습니다.

## **6.1 매직 값을 반환하지 말아야 한다**

**매직 값** magic value 은 함수의 정상적인 반환 유형에 적합하지만 특별한 의미를 가지고 있습니다. 그 예로는 값이 없거나 오류가 발생했음을 나타내기 위해 -1 을 반환하는 것이 있습니다.

### **6.1.1 매직 값은 버그를 유발할 수 있다**

과거에는 더 명시적인 오류 전달 기법이나 널 또는 옵셔널 타입을 반환하는 것이 가능하지 않거나, 실용적이지 않기 때문에 매직 값을 반환하는 것이 어느 정도 합리적인 이유가 있었습니다. 레거시 코드로 작업 중이거나, 신중하게 최적화해야 하는 코드가 있다면 여전히 이렇게 할 이유가 있습니다. 그러나 일반적으로 매직 값을 반환하면 예측을 벗어날 위험이 있으므로 사용하지 않는 것이 좋습니다. 아래 예제를 통해 매직 값이 어떻게 버그를 유발할 수 있는지 알아 보겠습니다.

```
class User {
  private final int age;
  ...

  // 나이가 주어지지 않는 경우에는 -1을 반환합니다.
  int getAge() {
    if(age == 0) {
      return -1;
    }
    return age;
  }
}
```

위 함수의 주석문을 확인하지 않고 유저의 평균 나이를 집계한다고 했을 때 함수는 정상적으로 돌아가고 결과 값에서도 이상한 점을 발견하기 힘들 것입니다. 만약 위 함수를 이용하여 주주들에게 보내는 회사의 연례 보고서에 사용자의 평균 연령에 대한 통계를 준비했다면 주가에 큰 영향을 미칠 수 있고 심각한 법적 결과를 초래할 수도 있습니다.
추가로 주의할 점은 단위 테스트로도 이 문제를 발견하지 못할 수 있다는 점입니다. `User` 클래스에 나이가 없다는 것을 알지 못한다면 테스트 코드 상에서도 나이가 없는 상황에 대한 대처는 되지 않을 수 있기 때문입니다.

### **6.1.2 해결책: 널, 옵셔널 또는 오류를 반환하라**

```
double getMeanAge(List<User> users) {
  if (users.isEmpty()) {
    return null;
  }
}
double sumOfAges = 0.0;
for (User user : users) {
  sumOfAges += Double.parseDouble(user.getAge()); // age가 null일 경우, 컴파일 에러
}
return sumOfAges / users.size();
```

이 문제를 좀 더 조직과 제품의 관점에서 본다면, 널이 가능한 유형을 반환하면 개발자들은 사용자들의 평균 연령을 계산하는 것이 처음에 생각했던 것만큼 간단하지 않다는 것을 깨달을 수 있습니다. 이 정보를 기반으로 요구 사항을 개선해야 할 수도 있기 때문에 개발자는 관리자나 제품팀에 이 사실을 알릴 수 있습니다.

## **6.3 예상치 못한 부수 효과를 피하라**

**부수 효과** side effect 는 어떤 함수의 호출이 함수 외부에 초래한 상태 변화를 의미합니다.

- 사용자에게 출력 표시
- 파일이나 데이터베이스에 무언가를 저장
- 다른 시스템을 호출하여 네트워크 트래픽 발생
- 캐시 업데이트 혹은 무효화

부수 효과가 예상되고 코드를 호출한 쪽에서 그것을 원한다면 괜찮지만, 부수 효과가 예상되지 않을 경우 놀라움을 유발하고 버그로 이어질 수 있습니다.
예상치 못한 부수 효과를 일으키는 것을 피하는 좋은 방법 중 하나는 애초에 부수 효과가 일어나지 않도록 하는 것입니다.

### **6.3.2 예기치 않는 부수 효과는 문제가 될 수 있다**

함수의 목적이 값을 가져오거나 읽기 위한 경우, 다른 개발자는 일반적으로 함수 호출이 부수 효과를 일으키지 않는다고 가정합니다. 아래 코드는 사용자 디스플레이의 특정 픽셀에서 색상을 가져오는 함수를 보여줍니다. 이 함수는 상대적으로 쉽고 부수 효과도 없는 것처럼 보이지만 픽셀 색상을 읽기 전에 캔버스를 다시 그리는 동작을 수행합니다.

```
class UserDisplay {
  private final Canvas canvas;

  public Color getPixel(int x, int y) {
    canvas.redraw();
    PixelData data = canvas.getPixel(x, y);
    return new Color(
      data.getRed(),
      data.getGreen(),
      data.getBlue());
  }
}
```

이와 같이 예상치 못한 부수 효과가 문제의 소지가 될 수 있는 몇 가지 경우가 있습니다.

### 부수 효과는 비용이 많이 들 수 있다

`canvas.redraw()` 를 호출하는 작업은 잠재적으로 비용이 상당히 많이 들 수 있으며 사용자의 디스플레이가 깜박거릴 수도 있습니다. 이는 버그로 해석할 수 있는 여지가 있어 바람직하지 않은 기능입니다.
만약에 사용자가 디스플레이의 스크린샷을 찍을 수 있는 기능을 추가한다고 가정해봅시다.

```
class UserDisplay {
  private final Canvas canvas;

  public Color getPixel(int x, int y) { ... }

  public Image captureScreenShot() {
    Image image = new Image(canvas.getWidth(), canvas.getHeight());
    for (int x = 0; x < image.getWidth(); ++x) {
      for (int y = 0; y < image.getHeight(); ++y) {
        iamge.setPixcel(x, y, getPixel(x, y));
      }
    }
    return image;
  }
}
```

위 코드에서 `captureScreenShot()` 함수는 `getPixel()` 함수를 호출하여 픽셀을 하나씩 읽습니다. 만약 하나의 `redraw()` 이벤트가 10밀리초가 걸리고 사용자 디스플레이가 400*700 픽셀(즉, 총 28만 픽셀)이라고 가정해보면 스크린샷 캡쳐 한번당 47분이 걸리고 28만번 깜박거릴 것입니다.

### **6.3.3 해결책: 부수 효과를 피하거나 그 사실을 분명하게 하라**

픽셀을 읽기 전에 `canvas.redraw()` 를 호출하는 것이 정말로 필요한지를 가장 먼저 질문해야 합니다. **예측 가능한 코드를 작성하기 위한 가장 좋은 방법은 애초에 부수 효과를 일으키지 않는 것입니다.**

픽셀을 읽기 전에 `canvas.redraw()` 를 호출해야 하는 경우, `getPixel()` 함수의 이름을 변경하여 이 부수 효과가 발생할 것이라는 점을 분명히 나타내야 합니다. 예를 들어 `redrawAndGetPixel()` 과 같은 이름으로 수정할 수 있습니다.

## **6.4 입력 매개변수를 수정하는 것에 주의하라**

### **6.4.1 입력 매개변수를 수정하면 버그를 초래할 수 있다**

만약 여러분이 친구에게 책을 빌려준 후에 돌려받았는데 몇 페이지나 찢어져 있고 여백에 메모가 휘갈겨져 있다면 아마 화가 많이 날 것입니다.  여기서 책은 입력 매개변수, 친구는 호출되는 함수로 볼 수 있습니다. 함수 호출 시 입력 매개변수를 전달했는데 해당 변수가 수정된다면 책을 훼손한 것과 같다고 볼 수 있습니다.

입력 매개변수를 수정하는 것은 함수가 외부의 무언가에 영향을 미치기 때문에 부수 효과의 또 다른 예라고 볼 수 있습니다. 함수는 매개변수를 통해 입력을 가져오거나 빌려와서 반환 값을 통해 결과를 제공하는 것이 일반적입니다. 따라서 대부분의 개발자는 입력 매개변수의 수정이 일어나지 않을 것이라고 예상하고 이 부수 효과가 일어난다면 깜짝 놀랄 것입니다.

아래 코드는 온라인 서비스를 판매하는 회사의 주문을 처리합니다. 이 회사는 신규 사용자에게 무료 평가판을 제공하고 `processOrders()` 함수는 지급 요청 송장의 발송과 주문한 사용자가 서비스를 사용할 수 있도록 설정하는 두 가지 일을 수행합니다.

```
public List<Invoice> getBillableInvoices(Map<User, Invoice> userInvoices, Set<User> usersWithFreeTrial) {
  userInvoices.removeAll(usersWithFreeTrial);
  return userInvoices.values();
}

public void processOrders(OrderBatch orderBatch) {
  Map<User, Invoice> userInvoices = orderBatch.getUserInvoices();
  Set<User> usersWithFreeTrial = orderBatch.getFreeTrialUsers();

  sendInvoices(getBillableInvoices(userInvoices, usersWithFreeTrial));
  enableOrderedServices(userInvoices);
}

public void enableOrderedServices(Map<User, Invoice> userInvoice) { ... }
```

`getBillableInvoices()` 함수는 송장에 대해 지급 요청이 가능한지 결정합니다. 사용자에게 무료 평가판이 없는 경우 송장은 지급 청구할 수 있습니다. 불행히도 `getBillableInvoices()` 함수는 실행 시 입력 매개변수 중 하나인 `userInvoices` 맵 자료구조에서 무료 평가판을 가지고 있는 사용자를 삭제합니다. 이로 인해 버그가 발생하는 이유는 `processOrders()` 가 나중에 `userInvoices` 맵을 재사용하기 때문입니다. 즉, 무료 평가판을 사용하는 유저는 어떤 서비스도 사용할 수 없습니다.

### **6.4.2 해결책: 변경하기 전에 복사하라**

입력 매개변수 내의 값을 어쩔 수 없이 변경해야 하는 경우에는 변경 전에 새 자료구조에 복사하는 것이 최상의 방법입니다.

```
public List<Invoice> getBillableInvoices(Map<User, Invoice> userInvoices, Set<USer> usersWithFreeTrial) {
  return userInvoices
    .entrySet
    .stream
    .fillter(entry -> !usersWithFreeTrial.contains(entry.getKey()))
    .map(entry -> entry.getValue())
    .toList();
}
```

값을 복사하면 메모리나 CPU, 혹은 두 가지 모두와 관련해 성능에 영향을 미칠 수 있습니다. 하지만 입력 매개변수의 변경으로 인해 발생할 수 있는 예기치 못한 동작이나 버그와 비교하면 성능이 크게 문제되지 않는 경우가 많습니다.
그러나 리스트나 배열을 정렬하는 경우 정렬해야 할 값의 개수가 아주 많다면, 복사본을 만드는 것보다는 원래의 리스트나 배열 내에서 변경을 가하면서 정렬하는 것이 훨씬 더 효율적일 수 있습니다. **이처럼 성능상의 이유로 입력 매개변수를 변경해야 하는 경우 함수 이름과 문서에 이러한 일이 발생한다는 것을 분명히 하는 것이 좋습니다.**

## **6.6 미래를 대비한 열거형 처리**

열거형에 대한 여러 의견과 상관없이, 코드에서 열거형을 접하게 될 가능성이 있고 어느 시점에서는 열거형을 다뤄야 할 가능성도 있습니다. 그 이유는 다음과 같습니다.

- 다른 사람의 코드의 결과를 사용해야 하며 어떤 이유로든 그들이 열거형을 즐겨 사용할 수 있다.
- 다른 시스템에서 제공하는 결과를 사용하고 있을 때 열거형은 종종 데이터 형식에서 유일하게 실용적인 옵션일 수 있다.

열거형을 처리해야 하는 경우 나중에 열거형에 더 많은 값이 추가될 수 있다는 점을 기억하는 것이 중요합니다. 이것을 무시하고 코드를 작성하면, 자기 자신 혹은 다른 개발자들의 예측을 벗어나는 좋지 않은 결과를 초래할 수 있습니다.

### **6.6.1 미래에 추가될 수 있는 열거 값을 암묵적으로 처리하는 것은 문제가 될 수 있다**

어떤 회사가 주어진 비즈니스 전략을 따를 경우 어떤 일이 일어날지 예측하는 모델을 개발했다고 가정해봅시다. 아래 코드에는 모델의 예측을 나타내는 열거형에 대한 정의가 포함되어 있습니다. 또한 모델 예측을 사용하여 안전한 결과인지 나타내는 함수도 포함됩니다.

```
public enum PredictedOutcome {
  COMPANY_WILL_GO_BUST,
  COMPANY_WILL_MAKE_A_PROFIT
}

public boolean isOUtcomeSafe(PriedictedOutcome prediction) {
  if (prediction == PredictedOutcome.COMPANY_WILL_GO_BUST) return false;
  return true;
}
```

`isOutcomeSafe()` 가 참을 반환할 경우 자동 시스템으로 인해 비즈니스 전략이 시작됩니다. 거짓을 반환하면 비즈니스 전략은 시작되지 않습니다.
위 코드는 열거 값이 2개인 동안에는 작동합니다. 그러나 만약 누군가가 새로운 열거 값을 추가한다면 상황은 심각하게 잘못될 수 있습니다.

```
public enum PredictedOutcome {
  COMPANY_WILL_GO_BUST,
  COMPANY_WILL_MAKE_A_PROFIT,
  WORLD_WILL_END
}

public boolean isOUtcomeSafe(PriedictedOutcome prediction) {
  if (prediction == PredictedOutcome.COMPANY_WILL_GO_BUST) return false;
  return true;
}
```

`WORLD_WILL_END` 라는 열거 값이 추가되었습니다. 이 값은 회사가 주어진 비즈니스 전략을 따를 경우 이 세계의 종말이 올 것으로 모델이 예측하고 있음을 나타냅니다.

`isOutcomeSafe()` 함수 정의는 열거형 정의에서 수백 줄 떨어진 코드이거나, 완전히 다른 파일 혹은 다른 패키지에 있을 수 있습니다. 따라서 어떤 개발자가 `PredictedOutcome` 에 값을 추가하면 그에 따라 `isOutcomeSafe()` 함수도 당연히 수정할 것이라고 가정하는 것은 위험합니다.

`isOutcomeSafe()` 함수가 수정되지 않으면 `WORLD_WILL_END` 예측에 대해 참이 반환되어 비즈니스 전략이 시작됩니다.

### **6.6.2 해결책: 모든 경우를 처리하는 스위치 문을 사용하라**

```
public enum PredictedOutcome {
  COMPANY_WILL_GO_BUST,
  COMPANY_WILL_MAKE_A_PROFIT,
  WORLD_WILL_END
}

public boolean isOutcomeSafe(PredictedOutcome prediction) {
  switch (prediction) {
    case COMPANY_WILL_GO_BUST:
      return false;
    case COMPANY_WILL_MAKE_A_PROFIT:
      return true;
  }
  throw new UncheckedException("Unhandled prediction: " + prediction);
}
```

위 코드에서 `WORLD_WILL_END` 값을 처리하지 않으면 예외가 발생되어 `isOutcomeSafe()` 함수도 수정해야 함을 알 수 있게 됩니다.

Java 에서는 전체 enum 값에 대해 switch 문에서 처리하지 않는 경우, 컴파일 에러가 발생되어 수정을 강제합니다.

```
public enum PredictedOutcome {
  COMPANY_WILL_GO_BUST,
  COMPANY_WILL_MAKE_A_PROFIT,
  WORLD_WILL_END
}

public static boolean isOutcomeSafe(PredictedOutcome prediction) {
    return switch (prediction) { // WORLD_WILL_END 값을 처리하지 않아 컴파일 에러 발생
        case COMPANY_WILL_GO_BUST -> false;
        case COMPANY_WILL_MAKE_A_PROFIT -> true;
    };
}
```

### **6.6.3 기본 케이스를 주의하라**

스위치 문은 일반적으로 처리되지 않은 모든 값에 대해 적용할 수 있는 `default` 케이스를 지원합니다. 열거형을 처리하는 스위치 문에 기본 케이스를 추가하면 향후 열거형 값이 암시적으로 처리될 수 있으며 잠재적으로 예기치 않은 문제와 버그가 발생할 수 있습니다.

```
public enum PredictedOutcome {
  COMPANY_WILL_GO_BUST,
  COMPANY_WILL_MAKE_A_PROFIT,
  WORLD_WILL_END
}

public static boolean isOutcomeSafe(PredictedOutcome prediction) {
    return switch (prediction) {
        case COMPANY_WILL_GO_BUST -> false;
        case COMPANY_WILL_MAKE_A_PROFIT -> true;
        default -> false;
    };
}
}
```

컴파일 에러도 발생하지 않고 `true` 여야 하는 열거형에 대해서도 제대로 처리할 수 없게 됩니다.

## **6.7 이 모든 것을 테스트로 해결할 수는 없는가?**

예상을 벗어나는 코드를 방지하기 위한 코드 품질 향상 노력에 반대하는 사람들이 가끔 있습니다. 테스트가 이러한 모든 문제를 잡아낼 것이기 때문에 이런 노력은 시간 낭비라는 것입니다. 필자의 경험으로 볼 때, 이것은 현실에서 별로 효과가 없는 다소 이상주의적인 주장입니다. 아래는 테스트만으로는 코드가 올바르게 작동한다는 것을 보증하기 어려운 이유입니다.

1. 어떤 개발자들은 테스트에 대해 그다지 부지런하지 않을 수도 있다. 즉, 여러분의 코드에 대해 자신이 가정한 것이 틀렸다는 것을 드러내기 위해 충분한 시나리오나 코너 케이스를 테스트하지 않는다.
2. 테스트가 항상 실제 상황을 정확하게 시뮬레이션하는 것은 아니다. 코드를 테스트하는 개발자는 의존 라이브러리를 `mock` 객체를 통해 테스트해야 할 수도 있다. 이 경우 목 객체가 어떻게 행동할 것인지 자신이 **생각하는 바대로** 프로그래밍한다. 개발자의 예상을 벗어나는 동작에 대해서는 테스트가 어려워진다.
3. 멀티스레딩 문제와 관련된 버그는 낮은 확률로 발생하고, 어느 정도 큰 규모에서 실행될 때만 나타날 수 있어서 테스트하기 어렵기로 악명 높다.

다시 한번 강조하자면 테스트는 매우 중요합니다. 아무리 많은 코드 구조화나 코드 계약에 대한 걱정도 고품질의 철저한 테스트를 대체할 수 없습니다. 그러나 필자의 경험상 그 반대 역시 사실입니다. 직관적이지 않거나 예상을 벗어나는 코드에 숨어 있는 오류를 테스트만으로는 방지하기 어렵습니다.

## **요약**

- 다른 개발자가 작성하는 코드는 종종 우리가 작성하는 코드에 의존한다.
    - 다른 개발자가 우리 코드의 기능을 잘못 해석하거나 처리해야 하는 특수한 경우를 발견하지 못하면, 우리가 작성한 코드에 기반한 그 코드에서 버그가 발생할 가능성이 크다.
    - 코드를 호출하는 쪽에서 예상한대로 동작하기 위한 좋은 방법 중 하나는 **중요한 세부 사항이 코드 계약의 명백한 부분에 포함되도록 하는 것**이다.
- 우리가 사용하는 코드에 대해 허술하게 가정을 하면 예상을 벗어나는 또 다른 결과를 볼 수 있다.
    - 예를 들어 열거형에 추가되는 새 값을 예상하지 못한 경우다.
    - 의존해서 사용 중인 **코드가 가정을 벗어날 경우, 코드 컴파일을 중지하거나 테스트가 실패하도록 하는 것이 중요**하다.
- 테스트만으로는 예측을 벗어나는 코드의 문제를 해결할 수 없다. 다른 개발자가 코드를 잘못 해석하면 테스트해야 할 시나리오도 잘못 이해할 수 있다.
