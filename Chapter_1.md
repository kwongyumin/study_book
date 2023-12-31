# 1. 코드는 어떻게 소프트웨어가 되는가?

> **"소프트 웨어 공학이 엄밀한 과학은 아니며, 절대적인 규칙 몇 가지로 요약할 수 없다는 점이다.”**

모든 프로젝트는 서로 다르며, 절충점을 찾아야 하는 사항들이 거의 모든 경우에 존재한다. 
좋은 코드를 작성하기 위해서는 당면한 상황에 합리적인 판단력을 적용해야 하고, 
어떤 특정한 행동 방식의 결과 (그것이 좋든 나쁘든)가 어떻게 될지 주의 깊게 생각해야 한다.
> 

## 1-1. 코드 품질이 소프트 웨어 품질에 미치는 영향

1. 고품질 코드
    
    <aside>
    ⭕ 신뢰할 수 있고 유지보수가 쉬우며 버그가 거의 없는 소프트 웨어
    
    </aside>
    
    1. 최초 요구 사항 : 완전하게 충족
    2. 요구 사항 변화 : 사소한 추가 작업 필요
    3. 오류 발생 시 : 시스템이 복구되거나 부분적으로 작동
    4. 처음 접하는 상황 : 명백히 예상되지 않은 상황도 처리
    5. 시스템 공격 : 시스템은 안전한 상태이고 손상되지 않음
2. 저품질 코드
    
    <aside>
    ❌ 신뢰할 수 없고 유지보수가 어려우며 버그가 너무 많은 소프트 웨어
    
    </aside>
    
    1. 최초 요구 사항 : 경계 조건을 처리하지 못하기 때문에 완전하게 충족하지 못함
    2. 요구 사항 변화 : 대규모의 코드 변경 및 리팩터링 필요
    3. 오류 발생 시 : 시스템은 미리 정의되지 않은 상태에 놓이고 데이터의 손상 가능성 존재
    4. 처음 접하는 상황 : 시스템은 미리 정의되지 않은 상태에 놓이고 데이터의 손상 가능성 존재
    5. 시스템 공격 : 시스템은 미리 정의되지 않은 상태에 놓이고 데이터의 손상 가능성 존재
    

## 1-2. 코드는 어떻게 소프트웨어가 되는가?

1. 개발자가 코드 베이스의 로컬 복사본을 가지고 작업하면서 코드를 변경한다.
2. 작업이 끝나면 코드 검토를 위해 변경된 코드를 가지고 병합 요청을 한다.
3. 다른 개발자가 코드를 검토하고 변경을 제안할 수 있다.
4. 작성자와 검토자가 모두 동의하면 코드가 코드 베이스에 병합된다.
5. 배포는 코드 베이스를 가지고 주기적으로 일어난다. 얼마나 자주 배포되는지는 조직과 팀마다 다를 수 있다.
6. 테스트에 실패하거나 코드가 컴파일되지 않으면 코드 베이스에 병합되는 것을 막거나 코드가 배포되는 것을 막는다.

## 1-3. 코드 품질의 목표

<aside>
🖊️ “코드를 고품질 혹은 저품질로 정의하는 것은 본질적으로 주관적이고 다소 성급한 것 이다.”
”좀 더 객관적이 되기 위해서는 한발짝 뒤로 물러서서 코드를 통해 정말로 달성하려는 것이 무엇인지 생각해보는 것이 유용하다는 것을 알게됐다.”

</aside>

1. 필자는 코드를 작성할 때 다음과 같은 네 가지 상위 수준의 목표를 달성하려고 한다.
    1. 작동해야 한다.
    2. 작동이 멈춰서는 안된다.
    3. 변화하는 요구 사항에 적응해야 한다.
    4. 이미 존재하는 기능을 또 다시 구현해서는 안 된다.

### 1-3-1. 코드는 작동해야한다.

1. 코드는 우리가 해결하려고하는 문제를 실제로 해결해야 한다. 
이것은 버그가 없다는 것을 의미하는데 버그가 존재하면 코드가 제대로 작동하지 않고 문제를 완전히 해결하지 못할 가능성이 있기 때문이다.

### 1-3-2. 코드는 작동이 멈추면 안된다.

<aside>
🖊️ "왜 코드가 작동을 멈출까?"
"코드는 고립된 환경에서 홀로 실행되는 것이 아니다. 주의하지 않으면 주변 상황이 바뀌면서 동작이 멈출 수 있다."

</aside>

1. 코드는 다른 코드에 의존할 수 있는데 그 코드가 수정되고 변경될 수 있다.
2. 새로운 기능이 필요할 때 코드를 수정해야 할 수도 있다.
3. 우리가 해결하려고 하는 문제는 시간이 지남에 따라 변경된다. 소비자 선호 , 비즈니스 요구 , 고려해야할 기술 등이 바뀔 수 있다.

> 현재는 잘 돌아가지만 이런 것 중 하나가 변경된 경우, 제대로 동작하지 않는다면 그 코드는 별로 유용한 코드가 아니다.
당장 돌아가는 코드를 만들기는 쉽지만 , 변화하는 환경과 요구사항에도 불구하고 계속 작동하는 코드를 만드는 것은 훨씬 더 어렵다.
> 

### 1-3-3. 코드는 변경된 요구 사항에 적응할 수 있어야 한다.

<aside>
🖊️ “한 번 작성하고 수정되지 않는 코드는 거의 없다.”
”몇달에서 부터 보통은 몇년 , 때로는 수 십년 동안 동일한 소프트웨어를 지속적으로 개발할 수 있다.”
”이 과정에서 요구 사항은 계속 변한다.”

</aside>

1. 비즈니스 환경이 변한다.
2. 사용자 선호가 변한다.
3. 가정이 더 이상 유효하지 않다.
4. 새로운 기능이 계속 추가된다.

### 1-3-4. 코드는 이미 존재하는 기능을 중복 구현해서는 안된다.

<aside>
🖊️ “문제를 해결하기 위해 코드를 작성할 때, 일반적으로 큰 문제를 여러 개의 작은 하위 문제로 나눈다.”

</aside>

**1.** 예를 들어 **이미지 파일을 로드하고 흑백 이미지로 변환한 다음에 다시 저장하는 경우** , 우리가 해결해야 할 하위 문제는 다음과 같다.

1. 파일에서 바이트 데이터를 로드한다.
2. 바이트 데이터를 분석해서 이미지 형식으로 만든다.
3. 이미지를 흑백으로 변환한다.
4. 이미지를 다시 바이트로 변환한다.
5. 바이트 데이터를 파일로 저장한다.

> 이 하위 문제 중 많은 것들은 이미 다른 사람이 해결한 상태다. 
예를 들어 파일에서 바이트 데이터를 로드하는 것은 대부분의 프로그래밍 언어에서 지원된다. 
파일 시스템과 하위 수준에서 통신하기 위한 코드를 작성하지는 않을 것이다. 
마찬가지로 바이트를 분석해서 이미지로 만드는 데 사용할 수 있 는 기존 라이브러리가 있을 것이다.
파일 시스템과 하위 수준의 통신 혹은 바이트 분석을 통한 이미지 생성 코드를 자신이 스스로 작성 한다면 이미 존재하는 기능의 중복 구현일 뿐이다.
> 

**2.** 기존 해결책을 무시하고 자기가 다시 작성하는 대신 이미 구현된 코드를 재사용하면 좋은 몇가지 이유가 있다.

1. 시간과 노력을 절약한다. 
    1. 파일을 로드하기 위해 프로그래밍 언어에서 지원하는 기능을 사용한다.
2. 버그 가능성을 줄여준다.
    1. 주어진 문제를 해결할 수 있는 기존 코드가 있다면, 그 코드는 이미 철저히 테스트했을 것이다. 
    2. 또한. 실제 서비스 환경에서 이미 사용되고 있을 것이고 그 코드에 버그가 있을 가능성은 작아진다. 
    왜냐하면 버그가 있었다면 이미 발견되고 해결되었을 가능성이 크기 때 문이다.
3. 기존 전문지식을 활용한다.
    1. 바이트 일부를 분석해서 이미지를 생성하는 코드를 관리하는 팀은 이미지 인코딩에 대한 전문가일 가능성이 크다. 
    2. 만약 IPEG 인코딩의 새로운 버전이 나온다면. 그것 에 대해 누구보다도 빨리 알고 자신들의 코드를 업데이트할 것이다. 
    3. 코드를 재사용함으로써 우리 는 그들의 전문지식과 향후 있을 업데이트를 받을 수 있다.
4. 코드가 이해하기 쉽다.
    1. 만약 표준화된 방법으로 코드 작성을 한다면 다른 개발자가 전에 그것을 접해봤을 가능성이 있다. 
    2. 대부분의 개발자는 아마도 과거에 파일을 읽는 코드를 작성해 봤을 것 이므로, 
    이와 같이 표준화된 방식으로 작성된 코드를 보면 그것이 어떻게 작동하는지 즉시 이해 할 것이다. 
    

## 1-4. 코드 품질의 핵심 요소

<aside>
🖊️ “방금 살펴본 네 가지 목표는 우리가 근본적으로 성취하고자 하는 것에 초점을 맞추는 데는 도움이 되지만” 
”일상적 업무로 하는 코딩 작업과 관련해서는 특별한 조언을 제공하지는 않는다.”
”이러한 목표 에 부합하는 코드를 작성하는 데 도움이 될 보다 구체적인 전략을 파악하는 것이 유용하다.”
”이 책은 6가지 전략을 중심으로 진행될 것이며, 필자는 이를 (과장된 용어로) '코드 품질의 여섯 가지 핵심 요소라고 부른다.”

</aside>

1. 코드는 읽기 쉬워야 한다.
2. 코드는 예측 가능해야 한다.
3. 코드를 오용하기 어렵게 만들라.
4. 코드를 모듈화하라.
5. 코드를 재사용 가능하고 일반화할 수 있게 작성하라.
6. 테스트가 용이한 코드를 작성하고, 제대로 테스트하라.

### 1-4-1. 코드는 읽기 쉬워야 한다.

<aside>
🖊️ "코드를 읽을 때 다음과 같은 사항을 이해하기 위해 애쓸 것 이다.”

</aside>

1. 코드가 하는 일
2. 어떻게 그 일을 수행하는지 
3. 입력이나 상태 등 어떤 것을 필요로 하는지
4. 코드 실행 결과물

> 코드를 작성하고 난 후 어느 시점이 되면 다른 개발자가 그 코드를 읽고 이해해야 하는 상황이 반드시 온다. 

코드가 병합되기 전에 코드 검토를 받아야 한다면, 그 일은 코드 작성 후 바로 일어날 것이다. 

하지만 코드 검토를 무시하더라도, 어느 순간 누군가가 자신의 코드를 이해하려고 에쓰는 상황이 오기 마련이다. 

예를 들어 요구 사항이 변경되거나 디버깅이 필요할 때 그런 상황이 올 수 있다.

코드의 가독성이 떨어진다면, 다른 개발자가 그 코드를 이해하는 데 많은 시간을 들여야 한다. 

또한, 코드의 기능에 대해 잘못 이해하거나 몇 가지 중요한 세부 사항을 놓칠 가능성 역시 크다. 

이런 일이 일어난다면 코드 검토 중에 버그를 발전할 가능성이 작고, 새로운 기능을 추가하기 위해 다른 사람 이 코드를 수정할 때 새로운 버그가 도입될 가능성이 크다. 

소프트웨어가 수행하는 모든 일은 그것 을 가능하게 하는 어떤 코드에 의해 일어난다. 그 코드가 무엇을 하는지 개발자가 이해하지 못 하면. 
소프트웨어 전체가 제대로 작동하는 것은 거의 불가능에 가깝다.
> 

### 1-4-2. 코드는 예측 가능해야 한다.

<aside>
🖊️ “도움이 되거나 똑똑한 일을 수행하는 코드를 작성할 때. “
”아무리 좋은 의도를 가지고 있더라도 예상을 벗어난 동작을 수행하는 위험이 있을 수 있다.” 
”코드가 예상에서 벗어나는 일을 한다면,"
”그 코드를 사용하는 개발자는 그 상황을 알지 못하거나 그 상황에 대치할 생각을 하지 못할 것이다.”

</aside>

1. 문제의 코드와는 전혀 상관없어 보이는 부분에서 명백하게 이상한 일이 발견되기 전까지 시스템은 계속 비정상적으로 작동한다.
2. 이런 코드는 사소한 오류를 일으킬 수도 있지만, 어떤 경우에는 중요한 데이터가 손상되는 재앙과도 같은 상황을 초래할 수도 있다. 
3. 코드가 예상을 벗어나는 일을 수행하지는 않는지 주의 깊게 살펴야 하고, 할 수 있다면 그런 코드를 작성하지 않도록 노력해야 한다.

### 1-4-3. 코드는 오용하기 어렵게 만들라

<aside>
🖊️ “TV 제조 업체는 전원 코드를 HDMI 소켓에 꽂는 것을 불가능하도록 만든다.”

</aside>

1. 누군가가 HDMI 소켓에 전원 케이블을 꽂으면 말 그대로 TV가 폭발할 수도 있다.
2. 자신이 작성하는 코드는 종종 다른 코드에 의해 호출되는데. 이것은 TV 뒷면과 비슷하다.  자신의 코드를 작성할 때 다른 코드가 어떤 것을 꽂기'를 기대한다. 
3. 즉. 호출할 때 인수가 입력되거나 시스템이 특정 상태에 있을 것을 예상한다. 자신이 작성한 코드에 잘못된 것들이 꽂히면, 모든 것이 폭발할 수 있다. 
시스템은 작동을 멈추고, 데이터베이스가 영구적으로 손상되거나. 중요한 데이터가 손실될 수 있다. 
4. 큰 문제가 일어나지는 않더라도 코드가 작동하지 않을 가능성이 크다. 자신이 작성한 코드가 호출된 데는 이유가 있는데, 
그 코드가 잘못 사용된다면 중요한 일이 수행되지 않거나, 이상하게 동작하지만 눈에 띄지 않는다는 것을 의미할 수도 있다.
5. 코드를 오용하기 어렵거나 불가능하게 하면 코드가 작동할 뿐만 아니라 계속해서 잘 작동할 가능성을 극대화 할 수 있다.

### 1-4-4. 코드를 모듈화하라

<aside>
🖊️ “모듈화는 개체나 시스템의 구성 요소가 독립적으로 교환되거나 교체될 수 있음을 의미한다."
”모듈화된 시스템의 주요 특징 중 하나는 인터페이스가 잘 정의되어 서로 다른 구성 요소 간 상호작용하는 지점이 최소화 된다는 점이다.”

</aside>

1. 소프트웨어 시스템과 코드베이스는 장난감과 매우 유사하다. 
2. 코드를 외부에 의존하지 않고 실행 할 수 있는 모듈로 나누는 것이 이로울 때가 많다. 이때 두 개의 인접한 모듈 사이의 상호작용은 한 곳에서 일어나고 잘 정의된 인터페이스를 사용한다. 
3. 이렇게 하면 변화하는 요구 사항에 더 쉽게 적응할 수 있는 코드를 작성하는 데 도움이 된다. 왜냐하면 한 가지 기능을 변경한다고 해서 다른 부분까지 변경할 필요가 없기 때문이다. 
4. 모듈화된 시스템은 일반적으로 이해하기 쉽고 추론하기 쉬운데 기능이 관리 가능한 단위로 나누어지고 기능 단위 간 상호작용이 잘 정의되고 문서화되기 때문 이다. 
5. 코드가 모듈화되어 작성되면 처음에 작동이 시작되고 그 후에도 계속해서 잘 작동할 가능성이 커진다. 왜냐하면 코드가 하는 일을 개발자들이 오해할 소지가 적기 때문이다.

### 1-4-5. 코드를 재사용 가능하고 일반화할 수 있게 작성하라

<aside>
🖊️ “재사용성과 일반화성은 유사하지만 약간 다른 개념이다.”

</aside>

1. 재사용성과 일반화성
    1. 재사용성 reusability
        1. 어떤 문제를 해결하기 위한 무언가가 여러 가지 다른 상황에서도 사용될 수 있음을 의미한다. 
        2. 핸드 드릴은 벽, 바닥 , 판 및 천장에 구멍을 뚫는 데 사용할 수 있기 때문에 재사용 가능한 도구다. 
        3. 문제는 동일하지만(드릴로 구멍을 뚫어야 한다). 상황은 다르다(벽을 뚫는 것과 바닥을 뚫는 것과 천장을 뚫는 것.)
        
    2. 일반화성 seneralizablity
        1. 개념적으로는 유사하지만 서로 미묘하게 다른 문제들을 해결할 수 있음 을 의미한다. 
        2. 핸드 드릴은 구멍을 뚫는 데 사용될 뿐만 아니라 나사를 박을 때도 사용될 수 있어 일반화성을 갖는다.
    
2. 코드는 적을 수록 좋다.
    1. 코드는 만들어 내는 데 시간과 노력이 필요하며. 일단 만들어지면 유지보수에 지속적인 시간과 노력이 들어간다. 
    2. 코드를 작성하고 이에 대한 대가를 받는 것이기 때문에 코드 라인이 적어야 한다는 말이 이상하게 들릴 수도 있다. 
    3. 하지만 실제로 우리는 코드를 많이 작성한 것에 대한 대가를 받는 것이 아니라 문제를 해결한 것에 대한 대가를 받는 것이다. 코드는 문제 해결을 위한 수단일 뿐이다.
    
3. 코드가 재사용할 수 있고 일반화되어 있으면 
    1. 우리는 그 코드를 코드베이스의 여러 부분에서, 그리고 하나 이상의 상황에서 사용할 수 있고 여러 가지 문제를 해결할 수 있다. 
    2. 이런 코드는 시간과 노력을 절약해주고 더 신뢰할 수 있는데 그 이유는 실제 서비스 환경에서 이미 시도되고 테스트된 논리를 재사용하기 때문이다. 
    3. 어떤 버그라 할지라도 이미 발견되고 해결되었을 가능성이 크다는 것을의미한다.
    4. 모듈화 된 코드 역시 더 높은 재사용성과 일반화성을 갖는다.

### 1-4-6. 테스트가 용이한 코드를 작성하고 제대로 테스트하라

<aside>
🖊️ “테스트는 이 두 프로세스에서 두 가지 핵심 사항에 대한 주된 방어 수단이 될 때가 많다.”

1. 버그나 제대로 동작하지 않는 기능을 갖는 코드가 코드 베이스에 병합되지 않도록 방지
2. 버그나 제대로 동작하지 않는 기능을 갖는 코드가 배포되지 않도록 막고 서비스 환경에서 실행되지 않도록 보장
</aside>

1. 테스트 (test): 이름에서 알 수 있듯이 코드 혹은 소프트웨어 전체를 테스트하는 것과 관련이 있다.
테스트는 수동이나 자동으로 수행된다. 개발자는 일반적으로 테스트 코드를 통해 “실제” 코드를 돌려보고 모든 것이 정상적으로 작동하는지 확인하고 , 이것을 자동화하기 위해 노력한다. 
여러 가지 수준의 테스트가 있는데 가장 흔히 볼 수 있는 테스트는 다음과 같은 세가지다.
    1. 단위 테스트 (unit test)
    일반적으로 개별 함수나 클래스와 같은 작은 단위의 코드를 테스트한다. 
    단위 테스트는 개발자가 일상 코딩에서 가장 자주 작업하는 수준의 테스트이고  이 책에서 유일하게 자세히 다룰 수 있는 수준의 테스트다.
    2. 통합 테스트 (Integation test)
    시스템은 일반적으로 여러 구성 요소, 모듈, 하위 시스템으로 구성된다. 
    이러한 구성 요소와 하위 시스템을 함께 연결하는 과정을 통합 mtegration이라고 한다.
    통합 테스트를 통해 이러한 여러 구성 요소들이 제대로 작동하고 멈추지 않고 계속 실행하는 지 확인할 수 있다.
    3. 중단간 (end to end , E2E Test)
    이 테스트는 처음부터 끝까지 전체 소프트웨어 시스템에서 작동의 흐름 (워크플로우)을 테스트한다. 
    테스트하는 소프트웨어가 온라인 쇼핑몰이라면 웹 브라우저를 자동으로 구동하고 사용자가 구매를 완료하는 데까지 진행할 수 있는지 테스트하는 것이 E2E 테스트의 예이다.
    
2. 테스트 용이성 (testabillty) : 이것은 테스트 코드와 반대의 테스트 대상이 되는 ‘실제’ 코드를 가리킨다.
해당 코드가 얼마나 테스트하기 적합한지를 나타낸다. 테스트 용이성은 서브 시스템이나 시스템 수준에도 적용될 수 있다. 테스트 용이성은 모듈화와 깊은 관련이 있으며, 모듈화된 코드(또는 시스템)은 테스트 용이성이 더 좋다.
    
    > 코드의 테스트 용이성이 낮으면 제대로 테스트하는 것이 불가능할 수 있다. 
    현재 작성 중인 코드의 테스트 용이성을 확인하기 위해 코드를 작성하면서 '어떻게 테스트할 것인가?'를 계속 자문하는 것이 좋다. 
    즉, 코드를 다 작성하고 나서 테스트에 대해 생각해서는 안 된다. 테스트는 코드 작성의 모든 단계에서 필수적이고 기본적인 부분이다.
    > 

## 1-5. 고품질 코드 작성은 일정을 지연시키는가?

<aside>
🖊️ “이 질문에 대한 답은 단기적으로는 고품질 코드를 작성하는 데 시간이 더 걸릴 수 있다는 것이다.” 
”높은 품질의 코드를 작성하는 것은 보통 우리 머릿속에 떠오르는 것을 바로 코딩하는 것보다 조금 더 많은 생각과 노력이 필요하다.” 
”하지만 한번 사용하고 버릴 사소한 유틸리티성 프로그램이 아닌 좀 더 중요한 소프트웨어를 개발하고 있다면. 일반적으로 고품질 코드를 작성하는 것이 중장기적으로는 개발 시간을 단축해준다.”

</aside>

1. 코드 품질을 고려하지 않고 먼저 떠오르는 대로 코딩하면 처음에는 시간을 절약할 수 있다.
    1. 그러나 이런 코드는 머지않아 취약하고 복잡한 코드베이스로 귀결될 것 이며. 점점 더 이해하기 어렵고 추론할 수 없는 코드가 된다.
    2. 새로운 기능을 추가하거나 버그를 수정하는 것이 점점 더 어려워지고 시간도 더 많이 걸리는데. 작동하지 않는 코드를 처리하고 재설계해야 하기 때문이다.
    3. '서두르지 않으면 더 빠르다'라는 말이 있다. 
    이 문구는 우리의 삶에서 무언가를 충분히 생각하거나 적절하게 하지 않고 너무 성급하게 행동하면  오히려 전반적인 속도가 늦어지는 실수를 초래할 때가 많다는 사실을 말해준다.
    4. '서두르지 않으면 더 빠르다'라는 말은 고품질 코드를 작성하는 것이 왜 시간을 절약하는길인지를 한 문장으로 잘 요약해준다.  **서두른다고 빨리할 수 있다고 착각하지 말자.**

# 2.요약

1. 좋은 소프트웨어를 만들려면 고품질 코드를 작성해야 한다.
2. 실제 서비스 환경에서 실행되는 소프트웨어가 되기 전에 코드는 일반적으로 여러 단계의 검사와 테스트를 통과해야 한다(때로는 수동, 때로는 자동화를 통해).
3. 버그나 제대로 동작하지 않는 기능이 사용자에게 제공되거나 비즈니스에 중요한 시스템에서 실행 되는 것을 이러한 검사를 통해 막을 수 있다.
4. 테스트는 코드를 작성하는 모든 단계에서 고려하는 것이 좋다. 코드를 다 작성하고 난 후에 고려 하는 것이 아니다.
5. 고품질 코드를 작성하면 처음에는 시간이 오래 걸리지만, 중장기적으로는 개발 시간이 단축되는 경우가 많다.
