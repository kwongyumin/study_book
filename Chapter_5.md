# **5. 가독성 높은 코드를 작성하라**

### **5.1.1 서술적이지 않은 이름은 코드를 읽기 어렵게 만든다.**

다음 코드는 이름을 지을 때 서술적인 이름을 짓기 위한 노력을 하지 않을 경우, 코드가 어떻게 보일지 보여주는 다소 극단적인 예시입니다.

```
import java.util.HashSet;
import java.util.List;
import java.util.Optional;
import java.util.Set;

public class BadReadability {

    class T {
        Set<String> pns = new HashSet<>();
        Optional<Integer> s = Optional.of(0);

        boolean f(String n) {
            return pns.contains(n);
        }

        Optional<Integer> getS() {
            return s;
        }

        Optional<Integer> s(List<T> ts, String n) {
            for (T t : ts) {
                if (t.f(n)) {
                    return t.getS();
                }
            }
            return Optional.empty();
        }
    }
}
```

만약 이 코드에 대해 설명하라는 요청을 받는다면 어떻게 답해야 할까요? 이 코드의 목적이 무엇인지, 코드에 있는 문자열, 정수, 클래스가 어떤 개념을 나타내는지 전혀 알 수 없습니다.

### **5.1.2 주석문으로 서술적인 이름을 대체할 수 없다.**

개선 방법 중 하나로 주석문 또는 문서를 추가하는 방법이 있을 수 있습니다.

```
import java.util.HashSet;
import java.util.List;
import java.util.Optional;
import java.util.Set;

public class BadReadability {

    /**
     * 팀
     */
    class T {
        Set<String> pns = new HashSet<>(); // 팀에 속한 선수의 이름
        Optional<Integer> s = Optional.of(0); // 팀의 점수

        /**
         * @param n 플레이어 이름
         * @return 플레이어가 팀에 속해 있는 경우
         */
        boolean f(String n) {
            return pns.contains(n);
        }

        /**
         * @return 팀의 점수
         */
        Optional<Integer> getS() {
            return s;
        }

        /**
         * @param ts 모든 팀의 리스트
         * @param n 플레이어의 이름
         * @return 플레이어가 속해 있는 팀의 점수
         */
        Optional<Integer> s(List<T> ts, String n) {
            for (T t : ts) {
                if (t.f(n)) {
                    return t.getS();
                }
            }
            return Optional.empty();
        }
    }
}
```

조금은 개선된 코드지만, 여전히 다음과 같은 문제가 있습니다.

1. 코드가 더 복잡해 보인다. **개발자는 코드뿐만 아니라 주석문과 문서도 유지 보수해야 한다.**
2. 코드를 이해하기 위해 파일을 **계속해서 위아래로 스크롤해야 한다.** T 클래스가 수백 줄의 길이라면, 굉장히 번거로운 일이다.
3. 함수 `s()`의 내용을 확인할 때 클래스 T를 살펴보지 않는 한 `t.f(n)`와 같은 호출이 **무엇을 하는지 또는 무엇이 반환되는지 알기 어렵다.**

### **5.1.3 해결책: 서술적인 이름 짓기**

```
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class GoodReadability {

    class Team {
        Set<String> playerNames = new HashSet<>();
        int score = 0;

        boolean containsPlayer(String playerName) {
            return playerNames.contains(playerName);
        }

        int getScore() {
            return score;
        }

        Integer getTeamScoreForPlayer(List<Team> teams, String playerName) {
            for (Team team : teams) {
                if (team.containsPlayer(playerName)) {
                    return team.getScore();
                }
            }
            return null;
        }
    }
}
```

## **5.2 주석문의 적절한 사용**

서술적인 이름으로 잘 작성된 코드는 그 자체로 줄 단위에서 무엇을 하는지 설명합니다. 코드의 기능을 설명하기 위해 낮은 층위의 주석문을 많이 추가해야 한다면, 그 자체로 코드 가독성이 떨어진다는 신호입니다.
반면에 **코드가 왜 그 일을 하는지에 대한 이유나 배경을 설명하는 주석문은 유용할 때가 많습니다.** 코드만으로는 이를 명확히 할 수 없기 때문이죠.

### **5.2.1 중복된 주석문은 유해할 수 있다.**

```
String generatedId(String firstName, String lastName) {
  // "{이름}.{성}"의 형태로 ID를 생성한다.
  return firstName + "." + lastName;
}
```

위 코드의 경우, 코드만으로도 이미 설명이 충분하기 때문에 주석문은 쓸모가 없습니다.

### **5.2.2 주석문으로 가독성 높은 코드를 대체할 수 없다.**

```
String generatedId(String[] data) {
  // data[0] : 유저의 이름, data[1] : 유저의 성
  // "{이름}.{성}"의 형태로 ID를 생성한다.
  return data[0] + "." + data[1];
}
```

이번에는 이름과 성이 배열의 첫 번째와 두 번째 요소로 주어지기 때문에 코드 자체만 보면 이에 대한 것을 알 수 없습니다. 따라서 이에 대해 설명하는 주석문이 추가된 모습니다. 코드 자체가 명확하지 않기 때문에 주석문이 유용한 것으로 보이지만 **진짜 문제는 코드가 읽기 쉽도록 작성되지 않았다는 점**입니다. 아래 코드로 확인할 수 있습니다.

```
String generatedId(String[] data) {
  return firstName(data) + "." + lastName(data);
}

String firstName(String[] data) {
  return data[0];
}

String lastName(String[] data) {
  return data[1];
}
```

### **5.2.3 주석문은 코드의 이유를 설명하는 데 유용하다.**

다음과 같은 경우에는 주석문을 사용해 코드가 존재하는 이유를 설명하면 좋습니다.

- 제품 또는 비즈니스 의사 결정
- 명확하지 않은 버그에 대한 해결책
- 의존하는 코드의 예상을 벗어나는 동작에 대처

```
public class User {

    private final int username;
    private final String firstName;
    private final String lastName;
    private final Version signupVersion;
    ...

    public String getUserId() {
        if (signupVersion.isOlderThan("2.0")) {
            // (v2.0 이전에 등록한) 레거시 유저는 이름으로 ID가 부여된다.
            // 자세한 내용은 #4128 이슈를 보라.
            return firstName.toLowerCase() + "." + lastName.toLowerCase();
        }
        // (v2.0 이후로 등록한) 새 유저는 username 으로 ID 가 부여된다.
        return String.valueOf(username);
    }
```

코드를 약간 지저분하게 만들지만, 이로 인해 얻는 이점이 더 큽니다. 주석문 없이 코드만으로는 혼란을 일으킬 수 있습니다.

### **5.2.4 주석문은 유용한 상위 수준의 요약 정보를 제공할 수 있다.**

```
/**
 * 스트리밍 서비스의 유저에 대한 자세한 사항을 갖는다.
 *
 * <pre>
 *     이 클래스는 데이터베이스에 직접 연결하지 않는다. 대신 메모리에 저장된 값으로 생성된다.
 *     따라서 이 클래스가 생성된 이후에 데이터베이스에서 이뤄진 변경 사항을 반영하지 않을 수 있다.
 * </pre>
 */
public class User {
  ...
}
```

위 주석문은 `User` 클래스가 전체적으로 수행하는 작업을 요약하기 위해 주석문을 사용합니다. '스트리밍 서비스' 의 사용자와 관련이 있고 잠재적으로 데이터베이스와 동기화되지 않을 수 있다는 사실과 같은 몇 가지 유용한 상위 수준의 정보를 제공합니다. 단점으로는 이런 주석문과 문서도 유지 및 보수가 필요하고 내용이 제때 업데이트되지 않으면 코드와 맞지 않게 되고 코드가 지저분해질 수 있습니다.

## **5.3 코드 줄 수를 고정하지 마라**

일반적으로 코드 줄 수는 적을수록 좋습니다. 코드의 줄이 많다는 것은 복잡성이 높다거나 재사용성이 낮다는 신호일 수 있습니다. 또한, 코드 줄이 많으면 읽어야 할 코드의 양이 늘어나기 때문에 인지 부하가 증가할 수 있습니다.

하지만 코드 줄 수는 우리가 실제로 신경 쓰는 것들을 간접적으로 측정해줄 뿐입니다. 우리가 정말로 신경써야 하는 것은 코드에 대해 다음과 같은 사항들을 확실하게 하는 것입니다.

- **이해하기 쉽다.**
- **오해하기 어렵다.**
- **작동이 안 되게 만들기가 어렵다.**

**이해하기 어려운 코드 한 줄**은 같은 일을 하는 **이해하기 쉬운 코드 10줄에 비해 코드 품질을 쉽게 낮출 수 있습니다.**

### **5.3.1 간결하지만 이해하기 어려운 코드는 피하라**

개발을 하다보면 코드의 줄수를 낮추기 위해 매우 간결한 단 한 줄의 코드로 압축하는 경우가 있습니다. 이때에 발생할 수 있는 문제는 다음과 같습니다.

- 다른 개발자는 이 단 한 줄의 코드에서 모든 세부 사항과 가정을 도출하기 위해 많은 노력을 기울여야 합니다. 이로 인해 시간 비용이 낭비되고, 코드를 오해하고 코드를 수정할 때 오류 발생 확률이 높아집니다.
- 명확하지 않고 문서화되지 않은 많은 가정으로 인해 코드 수정에 상당히 취약하고, 제대로 작동하지 않을 수 있습니다.
    
    코드의 줄 수가 많다는 것은 기존 코드를 재사용하지 않거나 무언가를 필요 이상으로 복잡하게 만들고 있다는 경고 신호가 될 수 있습니다. 따라서 추가되는 코드의 줄 수를 주시하는 것이 일반적으로는 바람직합니다. 그러나 이것보다 더 중요한 것은 **코드가 이해하기 쉬워야 하고, 어떤 상황에서도 잘 동작하며, 문제가 되는 동작을 할 가능성은 없는지 확인하는 것입니다.** 이것을 효과적으로 하기 위해 **더 많은 코드가 필요하다면 그것은 문제가 되지 않습니다.**
    

## **5.5 깊이 중첩된 코드를 피하라**

아래 예제는 차량 소유자의 주소를 찾아주는 함수를 보여줍니다.

```
public Optional<Address> getOwnersAddress(Vehicle vehicle) {
  if (vehicle.hasBeenScraped()) {
    return SCRAPYARD_ADDRESS;
  } else {
    Optional<Purchase> mostRecentPurchase = vehicle.getMostRecentPurchase();
    if (mostRecentPurchase == null) {
      return SHOWROOM_ADDRESS;
    } else {
      Optional<Buyer> buyer = mostRecentPurchase.getBuyer();
      if (buyer != null) {
        return buyer.getAddress();
      }
    }
  }
  return null; // 이 라인을 실행하는 경우를 생각하기가 쉽지 않다.
}
```

가독성이 떨어지고 특정 값이 반환되는 시점을 파악하기 위해 밀집된 모든 `if-else` 논리를 탐색해야 합니다.

### **5.5.1 해결책: 중첩을 최소화하기 위한 구조 변경**

중첩을 최소화하기 위해 구조를 변경해보겠습니다.

```
public Optional<Address> getOwnersAddress(Vehicle vehicle) {
  if (vehicle.hasBeenScraped()) {
    return SCRAPYARD_ADDRESS:
  }
  Optional<Purchase> mostRecentPurchase = vehicle.getMostRecentPurchase();
  if (mostRecentPurchase == null) {
    return SHOWROOM_ADDRESS;
  }
  Optional<Buyer> buyer = mostRecentPurchase.getBuyer();
  if (buyer != null) {
    return buyer.getAddress();
  }
  return null;
}
```

중첩된 모든 블록에 반환문이 있을 때, 중첩을 피하기 위해 논리를 재배치하는 것이 일반적으로 아주 쉽습니다.

### **5.5.2 중첩은 너무 많은 일을 한 결과물이다.**

아래 코드는 너무 많은 일을 하는 함수를 보여주는데 차량 소유자의 주소를 찾고, 그 주소를 이용해 편지를 보내는 두 가지 논리를 수행합니다.

```
public Optional<SentConfirmation> sendOwnerALetter(Vehicle veicle, Letter letter) {
  Optional<Address> ownersAddress = null;
  if (vehicle.hasBeenScraped()) {
    return SCRAPYARD_ADDRESS;
  } else {
    Optional<Purchase> mostRecentPurchase = vehicle.getMostRecentPurchase();
    if (mostRecentPurchase == null) {
      return SHOWROOM_ADDRESS;
    } else {
      Optional<Buyer> buyer = mostRecentPurchase.getBuyer();
      if (buyer != null) {
        return buyer.getAddress();
      }
    }
  }
  if (ownersAddress == null) {
    return null;
  }
  return sendLetter(ownersAddress, letter);
}
```

이 코드의 진짜 문제점은 함수가 너무 많은 일을 한다는 것입니다.

### **5.5.3 해결책: 더 작은 함수로 분리**

```
public Optional<SentConfirmation> sendOwnerALetter(Vehicle vehicle, Letter letter) {
  Optional<Address> ownersAddress = getOwnersAddress(vehicle);
  if (ownersAddress != null) {
    return sendLetter(ownersAddress, letter);
  }
}

public Optional<Address> getOwnersAddress(Vehicle vehicle) {
  if (vehicle.hasBeenScraped()) {
    return SCRAPYARD_ADDRESS:
  }
  Optional<Purchase> mostRecentPurchase = vehicle.getMostRecentPurchase();
  if (mostRecentPurchase == null) {
    return SHOWROOM_ADDRESS;
  }
  Optional<Buyer> buyer = mostRecentPurchase.getBuyer();
  if (buyer == null) {
    return null;
  }
  return buyer.getAddress();
}
```

## **5.6 함수 호출도 가독성이 있어야 한다**

어떤 함수의 이름이 잘 명명되면 그 함수가 무슨 일을 하는지 분명하지만 이름이 잘 지어졌더라도 함수의 인수가 무엇을 위한 것이고, 무슨 역할을 하는지 명확하지 않다면 함수 호출 자체가 이해되지 않을 수 있습니다.

> 많은 함수 인수
함수 호출은 인수의 개수가 늘어나면 이해하기 힘들어집니다. 함수나 생성자가 많은 수의 매개변수를 가지고 있으면 이것은 보다 근본적인 문제를 나타내는 것일 수 있습니다.
> 

### **5.6.1 매개변수는 이해하기 어려울 수 있다**

```
sendMessage("hello", 1, true);
```

위 함수는 인수가 무엇을 나타내는지 분명하지 않습니다.

```
void senMessage(String message, int priority, boolean allowRetry) {...}
```

함수 호출 시 각 인수의 값이 무엇을 의미하는지 알려면 함수 정의를 확인해봐야 합니다. 확인해보면 `1`은 메세지 우선순위를 나타내고 `true` 는 메세지의 전송이 실패할 경우 다시 전송을 시도한다는 의미라는 것을 알 수 있습니다.

### **5.6.2 해결책: 서술적 유형 사용**

`sendMessage()` 함수의 작성자는 우선순위를 나타내기 위해 정수를 사용했고, 재시도 허용 여부를 나타내기 위해 불리언 값을 사용했습니다. 정수와 불리언 값은 상황에 따라 어떤 종류의 값이라도 의미할 수 있기 때문에 그 자체로는 서술적이지 않습니다. 아래는 이를 해결하기 위한 두 가지 방법을 보여줍니다.

```
class MessagePriority {
  ...
  MessagePriority(int priority) {...}
  ...
}

enum RetryPolicy {
  ALLOW_RETRY,
  DISALLOW_RETRY
}

void sendMessage(String message, MessagePriority priority, RetryPolicy retryPolicy) {...}
```

```
sendMessage("hello", new MessagePriority(1), RetryPolicy.ALLOW_RETRY);
```

### **5.6.3 때로는 훌륭한 해결책이 없다.**

함수를 호출하는 라인의 가독성을 높여주는 특별한 방법이 없을 때가 있습니다. 아래와 같이 상자를 만드는 클래스가 있다고 가정해보겠습니다.

```
class Box {
  madeBox(int top, int bottom, int left, int right);
}
```

```
Box box = new Box(20, 20, 40, 30);
```

이 경우는 특별히 만족스러운 해결책이 없으며, 여기서 최선의 방법은 생성자를 호출할 때 각각의 인수가 무엇인지 설명하기 위해 인라인 주석문을 사용하는 것입니다.

```
Box box = new Box(
/* top = */ 20,
/* bottom = */ 20,
/* left = */ 40,
/* right = */ 30
);
```

가독성은 분명히 높이지만 이렇게 코드를 작성할 때 실수를 하지 않아야 하고, 최신 상태로 계속 유지해야 하므로 그다지 만족스러운 해결책은 아닙니다. 오히려 최신성을 유지하지 않는 경우, 더 큰 오류를 불러일으킬 수 있습니다.

`setter`함수를 추가하거나 빌더 패턴과 같은 것을 사용하는 것이 대안이 될 수는 있으나 두 방법 모두 값이 누락된 채 인스턴스가 만들어질 수 있기 때문에 코드가 쉽게 오용될 수 있습니다.

## **5.7 설명되지 않은 값을 사용하지 말라**

### **5.7.1 설명되지 않은 값은 혼란스러울 수 있다.**

```
class Vehicle {
  double getMassUsTon() {...}
  double getSpeedMph() {...}

  // 차량의 현재 운동에너지를 줄 단위로 반환
  double getKineticEnergyJ() {
    return 0.5 *
      getMassUsTon() * 907.1847 *
      Math.pow(getSpeedMph() * 0.44704, 2); // 거듭제곱
  }
}
```

위 코드는 미국 톤(`getMassUsTon()`) 단위를 킬로그램(kg)으로, 시간당 마일(`getSpeedMph()`)을 초당 미터(m/s) 로 변환 하기 위한 계수를 포함합니다. 하지만 이 계수가 의미하는 바가 코드에서 분명하지 않습니다.

### **5.7.2 해결책: 잘 명명된 상수를 사용하라**

```
class Vehicle {
  private final double KILOGRAMS_PER_US_TON = 907.1847;
  private final double METERS_PER_SECOND_PER_MPH = 0.44704;

  // 차량의 현재 운동에너지를 줄 단위로 반환
  double getKineticEnergyJ() {
    return 0.5 *
      getMassUsTon() * KILOGRAMS_PER_US_TON *
      Math.pow(getSpeedMph() * METERS_PER_SECOND_PER_MPH, 2);
  }
}
```

### **5.7.3 해결책: 잘 명명된 함수를 사용하라**

**헬퍼 함수 사용**

```
class Vehicle {
  ...
  // 차량의 현재 운동에너지를 줄 단위로 반환한다.
  double getKineticEnergyJ() {
    return 0.5 *
      usTonsToKilograms(getMassUsTon()) *
      Math.pow(mphToMetersPerSecond(getSpeedMph()), 2);
  }

  private double usTonsToKilograms(double usTons) {
    return usTons * 907.1847;
  }

  private double mphToMetersPerSecond(double mph) {
    return mph * 0.44704;
  }
}
```

상수나 헬퍼 함수를 다른 개발자들이 재사용할 것인지 고려해볼 만한 가치가 있다면 별도의 유틸리티 클래스에 두는 것도 좋습니다.

## **5.9 프로그래밍 언어의 새로운 기능을 적절하게 사용하라**

### **5.9.1 새 기능은 코드를 개선할 수 있다**

프로그래밍 언어 설계자는 새로운 기능을 추가하기 전에 매우 신중하게 생각하기 때문에 새로운 기능으로 인해 코드의 가독성이 높아지거나 코드가 더 견고해지는 경우가 많이 있습니다. 하지만 프로그래밍 언어의 그런 새로운 기능을 사용하고 싶은 마음이 간절히 들 때, 그것이 정말로 그 일에 가장 적합한 도구인지 솔직하게 생각해봐야 합니다. 아래는 문자열 리스트를 받아서 빈 것은 걸러내는 작업을 수행하는 함수를 전통적인 자바 코드로 작성한 예제입니다.

```
List<String> getNonEmptyStrings(List<String> strings) {
  List<String> nonEmptyStrings = new ArrayList<>();
  for (String str : strings) {
    if (!str.isEmpty()) {
      nonEmptyStrings.add(str);
    }
  }
  return nonEmptyStrings;
}
```

위 코드를 스트림을 사용해 작성하면 더 간결하고 이해하기 쉬운 코드가 됩니다.

```
List<String> getNonEmptyStrings(List<String> strings) {
  return strings
    .stream()
    .filter(str -> !str.isEmpty())
    .toList();
}
```

### **5.9.2 불분명한 기능은 혼동을 일으킬 수 있다**

프로그래밍 언어에서 제공하는 기능이 확실한 이점을 가지고 있다 하더라도 해당 기능이 다른 개발자에게 얼마나 잘 알려져 있는지 고려할 필요가 있습니다. 이를 위해서 **현재의 구체적인 상황과 궁극적으로 누가 코드를 유지보수할지에 대한 고민이 필요합니다.**

규모가 크지 않은 자바 코드를 유지보수하고 있고, 다른 개발자가 자바 스트림에 익숙하지 않다면 스트림을 사용하지 않는 것이 좋을 수도 있습니다. 이 상황에서는 스트림을 사용해서 얻을 수 있는 코드 개선이 그것이 초래할 혼란에 비해 상대적으로 미미할 수 있습니다.

## **요약**

- 코드의 가독성이 떨어져서 이해하기 어려울 때 다음과 같은 문제가 발생할 수 있습니다.
    - 다른 개발자가 코드를 이해하느라 시간을 허비함
    - 버그를 유발하는 오해
    - 잘 작동하던 코드가 다른 개발자가 수정한 후에 작동하지 않음
- 코드의 가독성을 높이다 보면 때로는 코드가 더 장황하게 되고 **더 많은 줄을 작성해야 할 수도 있습니다. 이것은 종종 가치 있는 절충**입니다.
- 코드의 가독성을 높이려면 **다른 개발자의 입장을 공감**하고, 그들이 코드를 읽을 때 **어떻게 혼란스러워할지를 상상**해보는 것이 필요합니다.
- 실제 시나리오는 다양하며 보통 그 상황에 해당하는 어려움이 있습니다. **가독성이 좋은 코드를 작성하려면 언제나 상식을 적용**하고 판단력을 길러야 합니다.
