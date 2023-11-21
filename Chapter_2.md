# 1. 추상화 계층

<aside>
🖊️ “코드를 구성하는 방법은 코드 품질의 기본적인 측면 중 하나이며”
”코드를 잘 구성한다는 것은 간결한 추상화 계층 (layers of abstraction)을 만드는 것으로 귀결될 때가 많다.”
”이 장에서는 이것이 무엇을 의미하는지 설명하고 , 문제를 추상화 계층으로 나누고 , 나눠진 추상화 계층을 반영하도록 코드를 구성하는 방법을 살펴볼 것 이다.”

</aside>

## 1-1. 널값 및 의사 코드 규약

<aside>
🖊️ “많은 프로그래밍 언어에는 값(또는 참조/포인터)이 없다는 개념을 가지고있다.“
”이 개념을 표현하기 위해 프로그래밍 언어는 널(null) 값을 사용한다. 
”이 널 값은 믿을 수 없을 정도로 유용하면서 동시에 믿을 수 없을 정도로 문제가 많은 양극단의 역사를 가지고있다.”

</aside>

1. 값이 제공되지 않거나 함수가 원하는 결과를 반환할 수 없는 경우가 자주 발생하기 때문에  ”값이 없다” 또는 “부재한다” 는 이 개념은 유용하다.
2. 값이 널일 수 있거나 혹은 널이면 안되는 경우가 항상 명백한 것은 아니라서 문제가 발생한다. 
또한, 개발자들은 변수에 액세스하기 전에 널 값 인지 확인하는 것을 자주 잊어버린다. 이 경우 오류가 발생하는 경우가 많다. 
**NullPointException, NullReferenceException, Cannot read property of null** 과 같은 에러를 본 적이 있을 것 이고 실제 자신이 기억하는 것 보다 많이 접했을것이다.

3. Null 안전성을사용하는예
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/66d4c8d7-b3a1-4224-acca-c4c74330cd76/Untitled.png)
    

---

## 1-2. 왜 추상화 계층을 만드는가?

<aside>
🖊️ "코드 작성은 복잡한 문제를 계속해서 더 작은 하위 문제로 세분화하는 작업이다.”

“소프트웨어 엔지니어로서 문제를 해결할 때 이것이 목표가 되어야한다.”

”비록 문제가 엄청나게 복잡할지라도 하위 문제들을 식별하고 올바른 추상화 계층을 만듦으로써 그 복잡한 문제를 쉽게 다룰 수 있다.”

</aside>

1. 사용자의 어떤 장치에서 실행되면서 서버에 메시지를 보내는 코드를 작성한다고 가정해보자.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c41d28eb-0bed-4a24-a15c-1f2cbed25970/Untitled.png)
    
    > 단 세줄이고 다음과 같은 네 가지 간단한 개념만 다루면 된다.
    > 
    > 1. 서버의URL
    > 2. 연결
    > 3. 메시지문자열보내기
    > 4. 연결닫기
    
2. 클라이언트 장치에서 서버로'Hello server'라는 문자열을 보내는 데는 다음과 같은 엄청나게 복잡한 일이 일어난다.
    1. 전송할수 있는 형식으로문자열 직렬화
    2. HTTP 프로토콜의모든복잡한동작
    3. TCP 연결
    4. 사용자의 장치가 와이파이 혹은 셀룰러 네트워크에 연결되어 있는지 여부 확인
    5. 데이터를 라디오 신호로 변조
    6. 데이터 전송 오류 및 수정
    
3. 하나의 문제가있을때 이 문제와 하위 문제에 대한 해결책이 일련의 층을 형성하고있는 것으로 생각 할 수 있다. 
최상위 계층에서는 HTTP 프로토콜이 어떻게 구현되는지 알 필요도 없이 서버에 메시지를 보내는 것에만 신경을쓰면서 코드를 작성할 수 있다. 
이와 비슷하게 HTTP 프로토콜을 구현하기 위한 코드를 작성한 엔지니어는 데이터가 무선 신호에 변조되는 방법에 대해 아무 것도 몰라도 문제가 없었을 것이다. 
HttpConnection 코드를 구현한 개발자는 물리적인 데이터 전송을 추상적인 개념으로 생각할 수 있었고, 우리 역시 HTTP 연결을 추상적인 개념으로 생각할 수 있다. 
이것을 추상화 계층 (layers of abstraction) 이라고 한다. 

### 1-2-1. 추상화 계층 및 코드 품질의 핵심요소

<aside>
🖊️ “깨끗하고 뚜렷한 추상화 계층을 구축하면 1장에서 살펴 봤던 코드 품질의 네 가지 핵심 요소를 달성할 수 있다.”

</aside>

1. 가독성
    1. 개발자들이 코드베이스에 있는 코드의 모든 세부사항을 이해하는 것은 불가능하지만 몇 가지 높은 계층의 추상화를 이해하고 사용하기는 상당히 쉽다. 
    깨끗하고 뚜렷한 추상화 계층을 만드는 것은 개발자가 한번에 한 두개 정도의 계층과 몇개의 개념만 다루면된다는 것을 의미한다. 
    따라서코드의 가독성이크게향상된다.
2. 모듈화
    1. 추상화 계층이 하위 문제에 대한 해결책을 깔끔하게 나누고 구현 세부 사항이 외부로 노출 되지 않도록 보장 할때, 
    다른 계층이나 코드의 일부에 영향을 미치지 않고 계층내 에서만 구현을변경하기가 매우쉬워진다. 
    
3. 재사용성 및 일반화성
    1. 하위 문제에 대한 해결책이 간결한 추상화 계층으로 제시되면 해당 하위 문제에 대한 해결책을 재사용하기가 쉬워진다. 
    그리고 문제가 적절하게 추상적인 하위 문제로 세분화된다면, 해결책은 여러가지 다른 상황에서 유용하게 일반화될 가능성이크다. 
    
4. 테스트 용이성
    1. 신뢰할 수 있는 코드를 작성하고자 한다면, 
    각 하위 문제에 대한 해결책이 견고하고 제대로 작동하는지 확인해야한다. 코드가 추상화 계층으로 깨끗하게 분할되면 각 하위문제에 대한 해결책을 완벽하게 테스트 하는 것이 훨씬 쉬워진다.
    

---

# 2. 코드의 계층

<aside>
🖊️ “실제로 추상화 계층을 생성하는 방법은 코드를 서로 다른 단위로 분할하여 단위 간의 의존 관계를 보여주는 의존성 그래프를 생성하는 것 이다.”

</aside>

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/806ab014-20ce-4a6d-8e46-3b4521337b78/Untitled.png)

1. 대부분의 프로그래밍 언어는 코드를 다른 단위로 나누기 위해 몇 가지 언어요소를 자유롭게 사용할 수 있다.
    1. 함수
    2. 클래스 (및 구조체나 믹스인과 같이 클래스와 비슷한 요소도 가능)
    3. 인터페이스 (또는 이와 동일한 요소)
    4. 패키지, 네임스페이스, 모듈
    

## 2-1. API 및 구현세부사항

<aside>
🖊️ “서비스 (service)를 구축하거나 호출하는 등의 작업을 해본 적이 있다면 애플리케이션 프로그래밍 인터페이스 (aplication programming interface), API라는 용어에 익숙할 것이다.“
”API는 서비스를 사용할 때 알아야 할 것들에 대한 개념을 형식화 하고, 서비스의 모든 구현 세부사항은 이 API 뒤에 감춘다.”

</aside>

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/45459959-9b99-4804-8ec3-b1c852468ac3/Untitled.png)

1. 코드를 작성할 때 고려해야 할 측면이 두가지 있다.
    1. 코드를 호출할 때 볼 수 있는 내용
        1. 퍼블릭 클래스, 인터페이스 및 함수(메서드)
        2. 이름, 입력 매개변수 및 반환 유형이 표현하고자 하는 개념
        3. 코드 호출시 코드를 올바르게 사용하기 위해 알아야하는 추가 정보(예: 호출순서)
    2. 코드를 호출할 때 볼 수 없는 내용
        1. 구현 세부 사항
        
2. API는 호출하는 쪽에 공개할 개념만 정의하면 되고 그 이 외의 모든 것은 구현 세부 사항이기 때문에 코드를 API의 관점에서 생각하면 추상화 계층을 명확하게 만드는데 도움이 된다. 
코드의 일부를 작성하거나 수정할 때, (입력 매개변수, 반환 유형, 퍼블릭 함수를 통해) API에 이 수정 사항에 대한 구현 세부 정보가 새어 나간다면  추상화 계층이 명확하게 구분되어 이루어진 것이 아니다.

## 2-2 함수

<aside>
🖊️ “어떤 로직을 새로운 함수로 구현하면 대부분 유익하다. 각 함수에 포함된 코드가 하나의 잘 써진 짧은 문장처럼 읽히면 이상적이다.”

</aside>

1. 너무 많은 일을하는 함수
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f7cb493f-938a-4ca3-b9e3-adb1b0b000fb/Untitled.png)
    
    > 이 함수는 차량 소유자의 주소를 조회하고, 주소가 발견되면 문자를보낸다. 
    이 함수는 문자를 보내기 위한 함수 호출과 소유자의 주소를 찾기 위한 자세한 논리를 모두 포함하고 있다. 너무 많은 개념을 동시에 다루기 때문에 코드가 이해하기 어렵다. 
    또한 단일 함수 내에서 너무 많은 작업을 수행하면 깊이 중첩된 if문과 같이 코드를 이해하기 어렵게 만드는 문제가 발생할 수 있다는 점도 확인할 수 있다.
    > 
    > 
    > “소유자의 주소(차량이 폐기된 경우 폐차장 주소. 차량이 아직 판매되지 않은 경우 전시장 주소, 그렇지 않으면 차량의 마지막 구매자의 주소)를 찾아 편지 한통을 보내라” 가 될 것 이다.
    > 
    > “차량 소유자의 주소를 찾아보고, 만약 발견되면, 그 주소로 편지를 보내라” 라는 문장으로 표현할 수 있는 함수라면 훨씬 더 좋을 것이다. 
    > 함수가 하는 일을 다음중 하나로 제한하면 이해하기 쉽고 단순한 문장으로 표현되는 함수를 작성하기  위한 좋은 전략이 될 수 있다.
    > 
    > 1. 단일업무수행
    > 2. 잘 명명된 다른 함수를 호출해서 더 복잡한 동작 구성
    > 
    > 일단 함수를 작성했으면 작성된 코드를 문장으로 만들어보면 좋다. 
    > 
    > 문장을 만들기 어렵거나 너무 어색하면 함수가 너무 길다는 것을 의미하고 더 작은 함수로 나누는 것이 유익할 것이다.
    > 
    
2. 더 작은 함수
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd13b298-82b7-489a-9433-54a865495080/Untitled.png)
    
    > 함수를 작게 만들고 수행하는 작업을 명확하게 하면 코드의 가독성과 재사용성이 높아진다. 
    코드를 마구 작성하다 보면 너무 길어서 읽을 수 없는 함수가 되기 쉽다. 따라서 코드 작성을 일단 마치고 코드 검토를 요청하기 전에 
    자신이 작성한 코드를 비판적으로 다시 한번 살펴보는 것이 좋다. 
    함수를 한 문장으로 표현하기 어렵게 구현 했다면 로직의 일부를 잘 명명된 헬퍼함수로 분리하는 것 을 고려 해봐야 한다.
    > 

## 2-3.  클래스

<aside>
🖊️ “개발자들은 단일 클래스의 이상적인 크기에 대해 논의하고 다음과 같은 많은 이론과 경험 법칙을 제시한다.”

![코드를 적절한 크기의 클래스로 쪼개지 않으면 너무 많은 개념을 한꺼번에 다루고, 가독성이 떨어지며 
모듈화가 덜 이루어지고, 재사용과 일반화가 어렵고, 테스트하기도 어려워진다.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5bb0d861-5594-4690-9667-72dd88ad15b2/Untitled.png)

코드를 적절한 크기의 클래스로 쪼개지 않으면 너무 많은 개념을 한꺼번에 다루고, 가독성이 떨어지며 
모듈화가 덜 이루어지고, 재사용과 일반화가 어렵고, 테스트하기도 어려워진다.

</aside>

1. 줄 수 (number of lines) : 때때로 ‘한 클래스는 코드 300줄을 넘지 않아야 한다’ 와 같은 가이드라인을 접하는 경우가 있다.
    
    > 이것은 어떤 것이 잘못되었을지도 모른다는 경고의 역할만 할 뿐, 어떤 것이 옳다는 보장은 아니다. 그러므로 이와 같은 규칙은 실제로 사용하기에는 상당히 제한적이다.
    > 
    
2. 응집력 (cohesion) : 이것은 한 클래스 내의 모든 요소들이 얼마나 잘 속해 있는지를 보여주는 척도로. 좋은 클래스는 매우 응집력이 강하다. 어떤 것들이 어떻게 결속되어 있는지 분류할 수 있는 방식이 많이 있는데 다음은 이에 대한 몇 가지 예다.
    1. 순차적 응집력은 한 요소의 출력이 다른 요소에 대한 입력으로 필요할 때 발생한다. 실제적인 예는 신선한 커피 한 잔을 만드는 과정일 것이다. 
    원두를 갈기 전에는 커피를 추출할 수 없다. 원두를 갈아내는 과정의 산출물은 커피를 추출하는 과정에 투입된다. 
    그러므로 우리는 갈고grinding 주출하는brewing 것 사이에 서로 응집력이 있다고 결론지을 수 있다.
    2. 기능적 응집력은 몇 가지 요소들이 모여서 하나의 일을 성취하는 데 기여할 때 발생한다. 
    하나의 일single task이 무엇인가에 대한 정의는 매우 주관적일 수 있지만, 실제 생활에서 예를 찾자면 케이크를 만들기 위해 필요한 모든 장비를 부엌의 전용 서랍에 보관하는 것이 될 수 있다. 
    반죽을 섞을 그릇, 나무 숟가락, 그리고 케이크 통은 케이크를 만들기 위해 모두 필요하며 함께 있어야 한다.
3. 관심사의 분리 (separation of concerns)
    
    > 이것은 시스템이 각각 별개의 문제(또는 관심사)를 다루는 개별 구성 요소로 분리되어야 한다고 주장하는 설계 원칙이다. 
    이것의 실제적인 예는 게임 콘솔이 TV와 동일한 제품으로 함께 묶이지 않고, TV와 분리되는 방식에서 찾아볼 수 있다. 게임기는 게임을 실행하는 것과 관련이 있고, TV는 동영상을 표시하는 것과 관련이 있다. 
    이렇게 두 가지가 분리되면 상황에 맞게 구성할 수 있다. 작은 아파트에 사는 사람은 게임기를 살 때 작은 TV를 위한 공간만 있는 반면, 더 넓은 공간을 가진 사람은 292인치 벽걸이 TV에 게임기를 꽂고 싶어 할 수도 있다. 
    이렇게 두 가지 항목이 분리되어 있으면 다른 항목을 업그레이드할 필요 없이 한 항목을 업그레이드할 수 있다. 더 새롭고 더 빠른 게임기가 출시되더라도 TV를 새로 다시 살 필요가 없다.
    > 

### 2-3-1 : 클래스 예제

1. 예제 : 너무 큰 클래스
    
    ```java
    class TextSummarizer {
    
            String summarizeText(String text) { return splitlntoParagraphs(text)
                    .filter(paragraph -> calculatelmportance(paragraph) >= IMPORTANCE_THRESHOLD)•join("\n\n");
            }
    
            private Double calculateImportance(String paragraph) {
                List<String> nouns = extractImportantNouns(paragraph); List<String> verbs = extractlmportantVerbs(paragraph); 
    						List<String> adjectives = extractlmportantAdjectives(paragraph); ... a complicated equation ...
                return importancescore;
    				}
            private List<String> extractImportantNouns(String text) { ... } 
    				private List<String> extractImportantVerbs(String text) { ... } 
    				private List<String> extractImportantAdjectives(String text) { ... }
    	
            private List<String> splitlntoParagraphs(String text) { 
    				List<String> paragraphs = [];
                Int? start = detectParagraphStartOffset(text, 0); 
    						while (start != null) {
                    Int? end = detectParagraphEndOffset(text, start); 
    								if (end == null) { break;}
                    paragraphs.add(text.subString(startj end));
                    start = detectParagraphStartOffset(textj end); 
    						}
            return paragraphs; }
    
            private Int? detectParagraphStartOffset( String text, Int fromOffset) { ... }
            private Int? detectParagraphEndOffset( String text, Int fromOffset) { ... }
          }
      }
    ```
    
    > 이 클래스를 작성한 사람과 이야기를 나눈다면, 이 클래스는 단지 한 가지 일, 즉 텍스트를 요약하는 것에만 관련이 있다고 주장할 것이다. 
    높은 층위에서 보자면 어느 정도 맞는 말이다. 하지만 이 클래스는 여러 하위 문제를 해결하는 코드를 가지고 있다.
    > 
    > 
    > ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0b0a8276-4d94-4c25-a0b9-3c258d4bb56b/Untitled.png)
    > 
    > 1. 텍스트를단락으로분할
    >     1. 텍스트문자열의중요도점수계산
    >     2. 이 하위 문제는 또 다시 중요한 명사,동사,형용사를 찾는 하위문제로 나뉜다.
    >     
    >     > 위와 같은 사실에 기초해 어떤 개발자는 이렇게 반박할지도 모른다. “아니요. 이 클래스는 여러 가지 다른 것들에 관련되어 있습니다. 이 클래스는 나누어져야 합니다.” 이 시나리오에서 두 개발자는 클래스가 응집력이 있어야 한다는 개념에는 동의한다. 
    >     하지만 관련 하위 문제를 해결하는 것이 원래 문제와는 다른 관심사인지 아니면 본질적으로 원래 문제의 일부분으로 간주하여야 하는지에 대해서는 의견이 다르다. 
    >     이 클래스가 분리되어야 할지 판단하기 위해서는 이 클래스가 어떻게 네 가지 핵심 요소에 반하여 작성되어 있는지 살펴보는 것이 더 나을 수도 있다. 
    >     이렇게 하면 다음과 같은 경우를 근거로 앞의 예제 코드의 클래스는 저품질 코드라는 결론을 내릴 수 있다
    >     > 
    
2. 예제 : 각 개념에 대한 별도의 클래스
    
    ```java
    class TextSummarizer {
            private final ParagraphFinder paragraphFinder;
            private final TextlmportanceScorer importanceScorer;
    				//클래스의 생성자를 통해 이 클래스가 의존하는 클래스의 인스턴스가 주입된다. 이것을 의존성 주입이라고 한다.
            TextSummarizer(ParagraphFinder paragraphFinder, TextlmportanceScorer importanceScorer) {
                this.ParagraphFinder = paragraphFinder;
                this.importanceScorer = importanceScorer;
            }
    				// 이 클래스를 사용할 때 기본 인스턴스를 생성하기 위한 정적 팩토리 함수
            static TextSummarizer createDefault() {
                return new TextSummarizer(
                        new ParagraphFinder(),
                        new TextImportanceScorer()
                );
            }
    
            String summarizeText(String text) {
                return ParagraphFinder.find(text)•filter(paragraph -> importanceScorer.islmportant(paragraph))
                        .join("\n\n");
            }
        }
    		// 하위 문제에 대한 해결책이 각자 다른 클래스로 나누어져 있다.
        class ParagraphFinder {
            List<String> find(String text) {
                List<String> paragraphs = [];
                Int ? start = detectParagraphStartOffset(text, 0); 
                while (start != null) {
                    Int ? end = detectParagraphEndOffset(text, start); 
                    if (end == null) { break; }
                    paragraphs.add(text.subString(start, end));
                    start = detectParagraphStartOffset(text, end);
                }
                return paragraphs;
            }
    
            private Int? detectParagraphStartOffset(String text, Int fromOffset) { ...}
            private Int? detectParagraphEndOffset(String text, Int fromOffset) { ...}
    
            class TextlmportanceScorer {
                Boolean isImportant(String text) {
                    return calculatelmportance(text) >= IMPORTANCE_THRESHOLD;
                }
                private Double calculateImportance(String text) {
                    List<String> nouns = extractlmportantNons(text);
                    List<String> verbs = extractlmportantVerbs(text);
                    List<String> adjectives = extractlmportantAdjectives(text);
                    return importancescore;
                }
    						// 주요 명사 찾기
                private List<String> extractImportantNouns(String text) { ...}
    						// 동사 찾기
                private List<String> extractImportantVerbs(String text) { ...}
    						// 형용사 찾기
                private List<String> extractImportantAdjectives(String text) { ...}
            }
        }
    ```
    
    > 이 코드를 읽기 위해서는 각 클래스마다 몇 개의 개념만 파악하면 되므로 코드의 가독성이 훨씬 좋아졌다고 할 수 있다. 
    TextSummarizer 클래스를 살펴보면 몇 초 만에 높은 층위의 알고리즘을 구성하는 모든 개념과 단계를 알 수 있다.
    > 
    > 
    > ![코드를 적절한 크기의 추상화 계층으로 나누면 한 번에 몇 가지 개념만 처리하는 코드를 만들 수 있다. 
    > 이를 통해 코드는 보다 더 읽기 쉽고, 모듈화되며, 재사용 가능하고, 일반화할 수 있고, 테스트가 쉬워진다.
    > ](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8a1e0420-468d-4d90-9467-0885a6554c94/Untitled.png)
    > 
    > 코드를 적절한 크기의 추상화 계층으로 나누면 한 번에 몇 가지 개념만 처리하는 코드를 만들 수 있다. 
    > 이를 통해 코드는 보다 더 읽기 쉽고, 모듈화되며, 재사용 가능하고, 일반화할 수 있고, 테스트가 쉬워진다.
    > 
    > 1. 단락을찾는다.
    >     1. 중요하지 않은 것은 걸러낸다.
    >     2. 남아있는 단락을연결한다.
    > 
    > 이 세 가지 단계가 우리가 알고 싶은 전부라면, 여기서 끝난다. 더 이상 할 것이 없다. 점수가 어떻게 계산되는지는 관심 없지만,
    >  단락을 어떻게 찾는지 알고 싶다면 ParagraphFinder 클래스를 통해 하위 문제가 어떻게 해결되는지 빠르게 파악할 수 있다.
    > 

## 2-4. 인터페이스

<aside>
🖊️ “계층 사이를 뚜렷이 구분하고 구현 세부 사항이 계층 사이에 유출되지 않도록 하기 위해 사용할 수 있는 한 가지 접근법은 어떤 함수를 외부로 노출할 것인지를 인터페이스를 통해 결정하는 것이다. “
“그 다음 이 인터페이스에 정의된 대로 클래스가 해당 계층에 대한 코드를 구현한다. “
“이보다 위에 있는 계층은 인터페이스에 의존할 뿐 로직을 구현하는 구체적인 클래스에 의존하지 않는다.“

</aside>

> 하나의 추상화 계층에 대해 두 가지 이상의 다른 방식으로 구현을 하거나 향후 다르게 구현할 것으 로 예상되는 경우 인터페이스를 정의하는 것이 좋다. 이전 절에서 살펴본 텍스트 요약 예제 코드를 생각해보자.
> 
> 
> 여기서 중요한 하위 문제 한 가지는 단락을 텍스트 요약에 포함할지 아니면 생략할지 결 정하기 위해 단락에 중요도 점수를 매기는 것이다. 
> 
> 원래 코드는 단락의 중요도를 계산하기 위해 단어 의 중요성을 고려하는 단순한 방법을 사용한다. 보다 강력한 접근법은 기계 학습을 사용하는 것인데, 단락이 중요한지 결정하는 모델을 학습할 수 있다. 
> 
> 이 방법은 아마도 실험해보고 싶은 것일 수도 있고, 먼저 개발 모드에서 사용해보고 선택적 베타 로 출시할 수도 있다. 
> 
> 기존 논리를 모델 기반 접근 방식으로 대체하려는 것이 아니라 두 가지 접근 방 식 중 하나로 코드를 구성하는 방법이 필요하다.
> 
1. 이를 위한 좋은 방법 중 하나는 TextlmportanceScorer 클래스를 인터페이스로 추출한 다음 이 하위 문제를 해결하기 위한 각각의 접근 방식을 서로 다른 클래스로 구현하는 것이다.
2. TextSummarizer 클래스는 TextlmportanceScorer 인터페이스에만 의존할 뿐 구체적인 구현 클래스에는 의존하지 않 는다. 그림 2.7은 서로 다른 클래스와 인터페이스 간의 의존성 측면에서 이것을 보여준다.

### 2-4-1. 인터페이스 예제

1. 인터페이스를 활용한 추상화 예제
    
    ```java
    interface TextImportanceScorer {
            Boolean isImportant(String text);
        }
    
        class WordBasedScorer implements TextImportanceScorer {
            @Override
            private Boolean isImportant(String text) {
                return caculateImportance(text) >= IMPORTANCE_THRESHOLD;
            }
    
            private Double calculateImportance(String text) {
                List<String> nouns = extractlmportantNouns(text);
                List<String> verbs = extractlmportantVerbs(text);
                List<String> adjectives = extractlmportantAdjectives(text);
                ...a complicated equation ••.
                return importanceScore;
            }
    
            private List<String> extractlmportantNouns(String text) { ...}
    
            private List<String> extractImportantVerbs(String text) { ...}
    
            private List<String> extractImportantAdjectives(String text) { ...}
        }
    
        // 새로운 모델 기반 점수 계산 클래스도 TextlmportantScorer 인터페이스률 구현한다.
        class ModelBasedScorer implements TextlmportanceScorer {
            private final TextPredictionModel model;
            static ModelBasedScorer create() { 
                return new ModelBasedScorer(TextPredictionModel.load(MODEL_FILE));
            }
            override Boolean islmportant(String text) { 
                return model.predict(text) >= MODEL_THRESHOLD;
            }
        }
    ```
    
    > 이제 TextSummarizer가 두 가지 팩토리 함수 중 하나를 사용하여 WordBasedScorer 또는 ModelBased Scorer를 사용하도록 구성할 수 있다.
    > 
    > 
    > ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fabbfc73-faab-46a0-8b9d-6d136e7cc9f2/Untitled.png)
    > 

1. Factory Method
    
    다음 예제 코드는 TextSummarizer 클래스의 인스턴스를 만 들기 위한 두가지 팩토리 함수를 보여준다.
    
    ```java
    TextSummarizer createWordBasedSummarizer() { return new TextSummarizer(new ParagraphFinder(), new WordBasedScorer());}
    TextSummarizer createModelBasedSummarizer() { return new TextSummarizer(new ParagraphFinder(), ModelBasedScorer.create());}
    ```
    
    > 추상화 계층을 깔끔하게 구현하는 코드를 만드는 데 있어 인터페이스는 매우 유용한 도구다.
    > 
    > 
    > 주어진 하위 문제에 대해 둘 이상의 서로 다른 구체적인 구현이 가능하고 이들 구현 클래스 사이에 전환이 필요할 때는 추상화 계층을 나타내는 인터페이스를 정의하는 것이 가장 좋다. 
    > 
    > 이를 통해 코드를 더욱 모듈화할 수 있고 재설정도 훨씬 쉽게 할 수 있다.
    > 

### 2-4-2. 모든 것을 위한 인터페이스?

<aside>
🖊️ “주어진 추상화 계층에 대해 한 가지 구현만 있고 향후에 다른 구현을 추가할 계획이 없더라도 여전히 인터페이스를 통해 추상화 계층을 표현해야 하는가는 여러분과 여러분의 팀이 결정할 사안이다.”
”몇 몇 소프트웨어 공학 철학은 이 상황에서도 여전히 인터페이스를 사용할 것을 권고한다.”

</aside>

이 권고를 따라 인터페이스 뒤로 TextSummarizer 클래스를 숨긴다면, 

이 경우 상위 코드 계층은 TextSummarizer라는 인터페이스에 의존할 뿐, TextSummarizerlmpl 구현 클래스에 직접 의존하지 않는다.

1. 하나의 인터페이스 및 단일 구현 예제 코드

```java
interface TextSummarizer { String summarizeText(String text); }

class TextSummarizerlmpl implements TextSummarizer {
	• • •
	override String summarizeText(String text) { return paragraphFinder.find(text)
		.filter(paragraph -> importanceScorer.isImportant(paragraph))
		•join("\n\n"); 
	}
}
```

> TextSummarizer의 구현은 단 하나뿐이며, 향후 다르게 구현할 필요가 있을지 현재는 알 수 없더라 도 이 접근 방식은 몇 가지 장점이 있다.
> 
> 1. 퍼블릭 API를 매우 명확하게 보여준다
>     1. 이 계층에서 사용해야 하는 기능과 사용하지 말아야 하는 기능에 대해 혼동할 일이 없다. 
>     개발자가 TextSummarizerlmpl 클래스에 새 퍼블릭 함수를 추가 하더라도 상위 계층은 TextSummarizer 인터페이스에만 의존하기 때문에 이 함수는 그 상위 계 층에 노출되지 않는다.
>     
> 2.  한가지 구현만 필요하다고 잘못 추측한 것 일 수 있다.
>     1. 원래 코드를 작성할 때는 또 다른 구현이 필요하지 않을 것이라고 확신 하더라도 한두 달 후에는 이러한 가정이 잘못된 것으로 판명될 수 있다. 
>     단지 몇 개의 단락을 생략하는 것으로 텍스트를 요약하는 것이 그다지 효과적이지 않다는 것 을 깨닫고, 완전히 다른 방식으로 텍스트를 요약하는 알고리즘을 실험하기로 할 수도 있다.
>     
> 3. 테스트를쉽게할수있다
>     1. 예를 들어 구현 클래스가 특별히 복잡하거나 네트워 크I/O에 의존하는 작업을 수행한다면 테스트 중에 목mock이나 페이크 객체로 대체할 수 있다. 
>     이렇게 하려면 사용 중인 프로그래밍 언어에 따라 반드시 인터페이스를 정의해야 할 수도 있다.
>     
> 4. 같은 클래스로 두 가지 하위 문제를 해결할 수 있다
>     1. 한 클래스가 두개 이상의 서로 다른 추상화 계층에 구현을 제공할 수도 있다. 
>     예를 들어 LinkedList 구현 클래스는 List 및 Queue 인터페이스를 모두 구현한다. 즉, 어떤 상황에서는 큐의 구현 클래스가 되고 다른 상황에서는 리스트의 구현 클래스로 사용될 수 있다. 
>     이렇게 하면 코드의 일반화 가능성을 크게 높일 수 있다.
>     

1. 인터페이스의 단점
    1. 더 많은 작업이 필요하다
        1. 인터페이스를 정의하려면 코드를 더 작성해야 한다(파일도 새로 필요할 것 이다).
        
    2. 코드가 복잡해질 수 있다.
        1. 다른 개발자가 코드를 이해하려고 할 때. 논리를 탐색하는 것이 어려울 수 있다. 
        어떤 하위 문제가 어떻게 해결되고 있는지 이해하기를 원한다면, 단지 하위 계층을 구현 하는 클래스로 직접 이동하는 대신, 인터페이스로 먼저 이동한 다음 그 인터페이스를 구현하는 구체적인 클래스를 찾아야 한다.
        
    
    <aside>
    📧 “필자의 개인적인 경험으로 볼 때 모든 클래스에 인터페이스를 붙이는 극단적인 입장의 코드는 종종 통제가 불가능하고, 불필요하게 복잡해지며, 이해와 수정이 어렵다.” 
    ”인터페이스를 사용할 경우 그 장점이 확실한 상황에서는 인터페이스를 사용하되. 인터페이스만을 위한 인터페이스를 작성해서는 안 된다.” 
    ”그럼에도 깨끗하고 뚜렷한 추상화 계층을 만드는 데 집중하는 것은 여전히 중요하다. 인터페이스를 정의하지 않더라도 클래스에서 어떤 함수를 퍼블릭으로 노출할지 매우 신중하게 생각해야 하며 구현 세부 사항이 유출되지 않도록 해야 한다.” 
    ”일반적으로 클래스를 작성하거나 수정할 때마다 나중에 필요한 경우 인터페이스를 붙이는 것이 어려워지지 않도록 코드를 작성해야 한다.”
    
    </aside>
    

## 2**-5.** 층이 너무 얇아질 때

<aside>
🖊️ “코드를 별개의 계층으로 세분화하면 장점이 많지만 다음과 같은 추가 비용이 발생한다.”

1. 클래스를 정의하거나 의존성을 새 파일로 임포트하려고 반복적으로 사용하는 코드 (boilerplate code) 로 인해 코드의 양이 늘어난다.
2. 로직의 이해를 위해 파일이나 클래스를 따라갈 때 더 많은 노력이 필요하다.
3. 인터페이스 뒤에 계층을 숨기게 되면 어떤 상황에서 어떤 구현이 사용되는지 파악하는데 더 많은 노력이 필요하다. 

---

“**이로 인해 로직을 이해하거나 디버깅하는 것이 더 어려워질 수 있다.”**

코드를 서로 다른 계층으로 분할해서 얻는 장점과 비교하면 이 비용이 상당히 낮은 것이지만, 분할을 위한 분할은 의미가 없다는 것을 명심해야 한다. 
비용이 이익보다 더 큰 시점이 올 수 있으므로 상식에 맞게 적용하는 것이 좋다.

</aside>

1. 너무 얇은 코드 계층 예제
    
    ```java
    class PragraphFinder{
            private final OffsetDetector startDetector;
            private final OffsetDetector endDetector;
            ...
    
            List<String> find(String text) {
                List<String> paragraphs = [];
                Int? start = startDetector.detectOffset(text, 0); 
                while (start != null) {
                    Int? end = endDetector.detectOffset(text? start); if (end == null) { break; }
                    paragraphs.add(text.subString(start, end));
                    start = startDetector.detectOffset(text, end); }
                return paragraphs; 
            }
        }
        
        interface OffsetDetector { 
            Int? detectOffset(String text, Int fromOffset);
        }
        class ParagraphStartOffsetDetector implements OffsetDetector { override Int? detectOffset(String text, Int fromOffset) { ... }
        }
        class ParagraphEndOffSetDetector implements OffsetDetector { override Int? detectOffset(String text, Int fromOffset) { ... }
            }
        }
    ```
    
    > 문제점 [ 1 ]
    
    이전에 본 ParagraphFinder 클래스를 시작과 종료 오프셋 파인더 클래스로 나누고 이 클래스들을 하나의 공통 인터페이스 뒤에 둠으로써 좀 더 많은 계층을 갖는 코드를 보여준다.
    ParagraphStartOffsetDetector 및 ParagraphEndOffSetDetector 클래스가
    ParagraphFinder 이 외의 다른곳에서 사용될 가능성은 별로 없기 때문에 이 코드의 계층이 너무 얇아졌다. 즉, 불필요하게 계층을 너무 잘게 쪼겠다.
    
    문제점 [ 2 ]
    ParagraphStartOffset Detector는 사용하면서 ParagraphEndOffsetDetector는 사용하지 않는 경우를 상상하기는 어렵다. 
    왜냐하면 이 구현 클래스들은 단락의 시작과 끝을 감지하는 방법에 대해 서로 밀접하게 연관되어있기 때문이다.
    > 
    
    <aside>
    🖊️ “코드 계층의 규모를 올바르게 결정하는 것은 중요하다. 코드베이스에 의미 있는 추상화 계층이 없으 면 전혀 관리할 수 없는 코드가 된다.**”
    ”**수십 년의 경험을 가진 개발자라 할지라도 추상화 계층을 올바르게 만들기 위해 코드베이 스로 병합하기 전에 코드의 설계와 재작업을 여러 번 반복해야 할 수도 있다는 점을 잊지 말자.”
    
    </aside>
    

## **2-6.** 마이크로서비스는어떤가?

<aside>
🖊️ “마이크로서비스를 사용할 때 코드 구조를 만들고 코드에 추상화 계층을 만드는 것은 중요하지 않다는 주장을 듣게 될 수도 있다.” 
”그 이유는 마이크로서비스 자체가 간결한 추상화 계층을 제공하기 때문이고, 내부의 코드가 어떻게 구성되고 나눠지는지는 중요하지 않다는 것이다.”

</aside>

1. 마이크로서비스 아키텍처에서는 개별 문제에 대한 해결책이 단지 단일 프로그램으로 컴파일되는 라이브러리 수준이 아니라 독립적으로 실행되는 서비스로 배포된다. 
즉, 시스템이 여러 개의 소규모 프로그램으로 분할되어 특정 작업만 전문적으로 수행한다. 이런 소규모 프로그램은 API를 통해 원격으 로 호출할 수 있는 전용 서비스로 배포된다. 
마이크로서비스는 여러 가지 이점을 가지고 있으며 최근 몇 년간 점점 더 인기를 끌고 있다. 이러한 아키텍처는 현재 많은 조직과 팀에서 가야할 방향으로 고정되고 있다.

2. 일반적으로 꽤 간결한 추상화 계층을 제공하는 것이 사실이지만, 대개 크기와 범위를 기준으로 마이크로서비스를 나누기 때문에 여전히 그 내부에서 적절한 추상화 계층을 고려하는 것이 유용하다.

### 2-6-1. 마이크로 서비스 예제

<aside>
🖊️ “이를 설명하기 위해 독자가 현재 온라인 소매업체에서 재고를 확인하고 수정하는 마이크로서비스의 개발과 유지보수를 담당하는 팀에 속해 있다고 가정해보자.”

</aside>

1. 이 마이크로 서비스는 다음 상황이 발생 하면 호출된다.
    1. 제품이 새로 창고에 도착한다.
    2. 사용자에게 그 제품을 보여주기 위해 프론트엔드에서 제품의 재고가 있는지 확인한다.
    3. 고객이 제품을 구매한다.
    
    > **명목상 이 마이크로서비스는 재고 수준 관리라는 한 가지 일만 한다. 그러나 이 ‘한 가지’를 수행하기 위해 해결해야 할 하위 문제가 많다는 것을 바로 알 수 있다.**
    > 
    > 1. 재고 항목의 개념,즉 실제로 추적하고 있는 것이 무엇인지 다루는 일
    > 2. 재고가 있는 창고 및 위치가 여러 군데일 때 처리하는 방법
    > 3. 재고가 있는 제품이라도 창고로부터 고객의 배송 주소까지의 거리에 따라 한 국가의 고객에겐 재고가 있을 수 있지만, 다른 국가의 고객에겐 재고가 없을 수 있다는 개념
    > 4. 실제 재고 수준 데이터가 저장된 데이터베이스와의 인터페이스 , 데이터베이스에 저장된 값의 해석

1. 문제를 해결할 때 하위 문제로 나누는 방법에 대해 이전에 언급했던 모든 내용이 마이크로 서비스 에도 여전히 적용된다. 예를 들어 특정 고객에 대해 어떤 품목이 재고가 있는지를 결정하는 것은 다음과 같은 하위 문제를 포함한다.
    1. 고객의범위내에있는창고결정
    2. 이 범위 내에 있는창고로부터 항목의 재고를찾기 위해 데이터 저장소에 질의
    3. 데이터베이스에서 반환되는 데이터 형식 해석
    4. 서비스 호출자에게 결과를 반환

<aside>
🔑 다른 개발자들도 이 논리를 일부 재사용하고 싶어 할 가능성이 크다.

회사 내에는 회사가 어떤 품목의 생산을 중단해야 할지, 재고를 더 많이 보유해야 할지, 특별 행사를 진행해야 할지 결정하기 위해 분석 및 추세를 추적하는 다른 팀이 있을 수 있다. 

효율성 및 시간 지연의 이유로 이 팀은 해당 마이크로서비스를 호출하는 대신 파이프라인을 사용하여 재고 데이터베이스를 직접 검색할 수 있다. 

하지만 데이터베이스에서 반환된 데이터를 해석하기 위한 노력이 여전히 필요할 수 있으므로 이 부분에 대한 코드를 재사용할 수 있다면 다른 팀에게 도움이 된다.

마이크로서비스는 시스템을 분리하여 보다 모듈화할 수 있는 매우 좋은 방법이지만, 서비스를 구현하기 위해 여러 하위 문제를 해결해야 한다는 사실은 바뀌지 않는다. 올바른 추상화 및 코드 계층을 만드는 것은 여전히 중요하다.

</aside>

# 3. 요약

1. 코드를 깨끗하고 뚜렷한 추상화 계층으로 세분화하면 가독성, 모듈화, 재사용, 일반화 및 테스트 용이성이 향상된다.
2. 특정 언어에 국한된 기능뿐만 아니라 함수, 클래스 및 인터페이스를 사용하여 코드를 추상화 계층으로 나눌 수 있다.
3. 코드를 추상화 계층으로 분류하는 방법을 결정하려면 해결 중인 문제에 대한 판단과 지식을 사용해야 한다.
4. 너무 비대한 계층 때문에 발생하는 문제는 너무 얇은 계층 때문에 발생하는 문제보다 더 심각하다. 확실하지 않은 경우에는 남용의 위험에도 불구하고 계층을 얇게 만드는 것이 좋다.
