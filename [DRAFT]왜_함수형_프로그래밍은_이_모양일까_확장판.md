# 왜 함수형 프로그래밍은 이 모양일까? - 확장판

## 서문 (확장판)
본 글은 지인으로부터 [왜 함수형 프로그래밍은 이 모양일까?](2022-11-20-왜_함수형_프로그래밍은_이_모양일까.md)에 대한 피드백을 받고 작성하는 확장판이다. 기존 글의 분량은 내용을 크게 바꾸지 않는 선에서 읽기 좋도록 수정하고, 마지막 부분에서 타입에 대한 부분이나 Continuation에 관련된 부분을 조금 더 확장했다. 이렇게 하는 가장 큰 이유는 물론 피드백을 받았기 때문이지만, 이전 글을 서두르듯 마무리 지은 것 같은 느낌이 아쉽기 때문이기도 하다.

처음 함수형 프로그래밍을 배울 때만 해도 왜 할당이 아니라 바인딩인지, 왜 불변인지, 왜 1급 시민으로서의 함수가 그렇게 중요한지에 대해 궁금했던 기억이 난다. 이러한 것을 공부하는데 있어서 커뮤니티에서 많은 것을 받은 만큼, 이 글로 나도 커뮤니티에 환원할 수 있으면 좋겠다는 생각을 한다.

## 서문 (원문)
본격적으로 개발을 업으로 삼고자 결심했던 즈음, Coursera의 Scala 코스로 좀 더 본격적인 프로그래밍에 입문하게 된 기억이 난다. (당시 강좌는 2022-12-15 현재 [Functional Programming in Scala Specialization](https://www.coursera.org/specializations/scala)으로 확장되었다) 당시에는 R이나 Python, Ruby 정도의 언어만 조금 만져본 것이 전부였던지라 따라가기 조금 버겁기는 했으나 계속 하다보니 생각보다 재미있어서 끝까지 마칠 수 있었고, 나중에는 Scala를 사용하는 회사에 취직까지 성공적으로 할 수 있었다. 프로그래밍이 성격에도 잘 맞고 Scala도 재미있고 직장도 잡았으니 모든 것이 잘 풀렸다. 하지만 그 당시부터 최근까지(대략 6년 이상) 내 머리 속을 떠나지 않은 궁금증이 하나 있으니, 그것은 바로 "함수형 프로그래밍이란 무엇일까?"라는 것이다. (물론 당시에는 그냥 궁금증 정도로 그냥 인터넷만 조금 뒤져보는 정도였지만, 모 커뮤니티 사이트에서 모 유저와 키배를 떠보고자 영상도 찾아보고 페이퍼도 읽어보는 등 더 열심히 공부하게 되긴 했다. Keyboard-battle driven study라고 하도록 하자.)

함수형 프로그래밍, 혹은 함수형 패러다임이 여러 분야에서 유행을 타기 시작하면서 이에 대한 정보를 찾아보기는 점점 더 쉬워지고 있다. 하지만 생각 외로 함수형 프로그래밍에 대한 명확한 정의를 찾아보기가 쉽지는 않은데, 마이클 포거스와 크리스 하우저의 저서 `클로저 프로그래밍의 즐거움`에서는 다음과 같은 구절도 나온다.

> 무엇이 함수형 프로그래밍인가? 틀렸다.

이 말이 무엇을 의미하는가? 그만큼 함수형 프로그래밍에 대한 정의가 제대로 이뤄지지 않고 있다고 볼 수 있지 않을까? 하지만 이렇게 정의가 어렵다는 것과 별개로 커뮤니티에서는 대개 함수형 프로그래밍을 다음과 같은 특징으로 정의하고는 한다.

- 1급 시민으로서의 함수 (First Class Function)
- 고차 함수 (Higher Order Function)
- 순수 함수 (Pure Function)
- 불변 (Immutability)
- 변수 할당 없음 (No Assignment)
- 참조 투명성 (Referential Transparency)

여기에 안전한 타입 시스템이나 Maybe/Option과 같은 펑터(Functor)/모나드(Monad) 개념을 더해 함수형 프로그래밍에 대해 이야기하는 것을 매우 흔하게 찾아볼 수 있다. 분명 틀렸다고 하지는 않겠다. 하지만 이 모든 것들을 종합했을 때 함수형 프로그래밍이 무엇인지 정의한다고 정말 이야기할 수 있을까?

정의의 문제와 별개로, 함수형 프로그래밍을 깊게 공부하는 사람들 사이에서는 부수 효과를 안전하게 다루는 것, 그리고 그를 위해 모나드를 사용하는 것을 어렵지 않게 찾아볼 수 있는데, 이러한 담론은 함수형 프로그래밍에 살짝 관심을 갖는 사람들에게 이러한 오해를 낳고는 한다.

- 함수형 프로그래밍은 어렵다 (Moand, Functor 등등 범주론을 적극적으로 활용하므로)
- 함수형 프로그래밍은 부수 효과(Side Effect)를 다루는 데 특화되었다 (순수 함수가 권장되며 부수 효과는 별도로 관리하게 하므로)
- 함수형 프로그래밍은 타입에 관한 것이다
- 2010 ~ 2020년대에 다른 언어들이 함수형 프로그래밍을 받아들이는 것은 결국 프로그래머들을 유치하기 위한 수단이며 이는 다른 함수형 프로그래밍 커뮤니티에 부정적인 영향을 줄 것이다 (필자가 진짜로 봤다)

앞서 말했듯, 이러한 모든 것들은 사실 오해에 불과하다는 것을 강조하고 싶다. 함수형 프로그래밍의 핵심은 매우 간단하다.

사실 영문 위키페디아의 [Functional programming 항목](https://en.wikipedia.org/wiki/Functional_programming)이 첫 문단이 함수형 프로그래밍의 명확한 정의를 보여준다

> In computer science, functional programming is a programming paradigm where programs are constructed by applying and composing functions. It is a declarative programming paradigm in which function definitions are trees of expressions that map values to other values, rather than a sequence of imperative statements which update the running state of the program.

> 컴퓨터 과학에서 함수형 프로그래밍은 함수의 조합과 적용을 통해 프로그램을 구성하는 프로그래밍 패러다임이다. 이는 프로그램의 상태를 업데이트하는 일련의 명령형 구문이 아닌, 함수의 정의가 값을 다른 값으로 매핑하는 표현식 트리인 선언형 프로그래밍 패러다임이다.

이 정의는 함수형 프로그래밍의 핵심 부분을 정의하지만, 정작 실제 프로그래밍에서 이것이 어떻게 적용되었는지 찾아보기는 쉽지 않다.

이 글의 목적은 매우 명확하다: 함수형 프로그래밍의 핵심에 대한 명확한 정의를 내리는 것. 함수형 프로그래밍의 특징은 정의를 내리는 도중 도출될 수는 있으나 주요 주제는 아니며, 함수형 프로그래밍의 장점은 인터넷이나 서적으로 이미 잘 나와있으므로 이 글에서 다루지 않는다.

## 함수형 프로그래밍의 정의

이 글에서 내리는 함수형 프로그래밍의 정의는 다음과 같다.

> 함수형 프로그래밍 혹은 패러다임은 수학적 표현식으로 구성된 프로그램을 작성하는 것을 의미한다.

## 수학적 표현식에서 프로그래밍 언어로

프로그래밍, 그리고 그 기반이 된 수학과 논리학이 역사적으로 서방 세계에서 발달되었다. 따라서, 안타까운 사실이나, 함수형 프로그래밍의 정의를 이해하기 위해서는 일단 서방 세계에서의 수학 식에 대해서 알아봐야 한다.

아래에 영어로 된 간단한 수학적 표현이 있다.

> $3 + x = 12$

이 방정식은 그 자체로 무언가를 표현하지만 이것이 참인지 거짓인지는 아직 알 수 없다. 그렇다면 단서를 주면 어떨까?

> $3 + x = 12$ $where$ $x = 9$

이 수학 표현식 읽고 이해하는 것은 그리 어렵지 않다. $x$가 9일 때(where), $x + 3 = 12$라는 수학 표현식은 참이다. 간단하지 않은가? 중요한 점은, 이 수학 표현식을 작성하는 다른 방법이 있다는 것이다. `let`과 `in`으로 `where` 부분을 앞으로 옮겨오는 것이다.

> $let$ $x = 9$ $in$ $3 + x = 12$

어딘가 기시감이 들지 않는가? 줄 바꿈으로 조금만 변형을 줘보자.

```fsharp
let x = 9 in
3 + x = 12
```

이렇게 줄 바꿈까지 적용한 코드는 이제 대부분의 독자들에게 조금 더 읽기 편할 것이다. Javascript나 Rust와 비슷해보이지 않는가? 사실 위의 코드는 실제로 동작하는 코드로, ML(Meta Language)에 영향을 받은 거의 현대 프로그래밍 언어 대부분(OCaml, F#, Haskell)에서 동작한다. (ML 계열 언어에서 `=`은 다른 언어의 `==`와 같이 동작한다. 따라서 위 식의 결과값은 `true`다)

독자의 첫 ML(Meta Language) 코드 작성을 축하하는 바다.

위의 코드, 혹은 수학 표현식이 사실 위키페디아에서 이야기한 "수학적 표현식"이며, 동작하는 함수형 프로그램은 사실 이 수학적 표현식이 거대해진 것에 불과하다.

이러한 수학적 표현식의 컴퓨터 프로그래밍에의 적용은 Peter Landin의 1966년 페이퍼, `The next 700 programming languages`에서 ISWIM이라는 언어로 처음 소개되었다. ISWIM은 이후 수많은 함수형 언어들에 영향을 주게 되는데, 그 언어들 중에는 ML과 HOPE등이 있으며, 이 언어들은 이후 OCaml, F#, Haskell 등의 언어에 다시 영향을 주게 된다.

### 표현식 뜯어보기

`let ... in ...` 혹은 `... where ...`로 표현된 표현식은 람다 대수(Lambda Calculus)의 표현식으로 다시 표현될 수 있다.

> $(\lambda x.3 + x = 12)$ $9$

이 글은 람다 대수에 관한 글은 아니므로 문법에 대한 자세한 설명은 생략하나, 위의 람다 대수 표현식을 대부분의 독자에게 익숙할(익숙...하시죠?) 자바스크립트 문법으로 다시 작성해보면 다음과 같다.

```javascript
((x) => 3 + x === 12)(9) // true
```

`let ... in ...` 표현식에서는 분명 변수를 x에 할당했던 것 같은데, 자바스크립트로 옮겨보니 변수 할당은 온데간데 없고 함수를 선언하고 바로 인자를 넣는 표현식만 남았다. 이게 무슨 의미인가? 이 식은 너무 간단하니 이해를 위해 식을 아주 조금만 더 복잡하게 만들어보자.

> $x + y = 12$ $where$ $y = x * 3$ $where$ $x = 3$

(수학적 표현식으로 보면 이상할 수도 있지만, ISWIM에서는 허용되는 문법이다 - where expression은 main expression과 where clause로 구성되며, 중첩이 가능하다.)

```ocaml
let x = 3 in
let y = x * 3 in
x + y = 12
```

```javascript
(
  (x) =>
  ((y) => 
    x + y === 12
  )(x * 3)
)(3)
```

이 표현식에서 알 수 있는 사실은 무엇일까?

함수가 호출될 때 종속 변수가 이미 주어졌다. 그렇다면 여기서 문제, 한 수학 함수의 내부에서 종속 변수의 값은 바뀔 수 있을까, 없을까? 답은 "바뀔 수 없다"다. 수학적으로 종속 변수의 값을 바꿀 방법따위는 존재하지 않으니, 함수형 프로그래밍에서도 그것은 불가능하다. 따라서 함수형 프로그래밍에서 다루는 모든 자료형은 기본적으로 불변 성질을 띄게 된다. 

다른 주제로는 할당에 관한 것이 있다. 함수형 프로그래밍에 대한 글을 읽다보면 항상 변수 "할당"이 없다는 이야기를 한다. 문법으로 보기에는 분명 변수 할당인데 그게 아니라고 하니, 이게 무슨 아버지를 아버지라 부르지 못하는 상황이란 말인가? 하지만 위의 표현식을 보면 이에 대한 설명이 가능하다. 우리가 작성하는 코드는 사실 함수의 연쇄며, 변수 할당을 한다고 생각한 모든 문법은 사실 내부에 선언한 다른 함수에 인자를 넘기는 것이다. 따라서 이 함수들을 수학적으로 접근했을 때, 우리가 보는 변수는 엄밀히 말해 종속 변수(bound variable)가 된다. Bound variable? 그렇다, 함수형 프로그래밍에 대한 글에서 이야기하는 그 바인딩이 바로 함수에 종속 변수로 넣는다는 이야기다. 이미 계산한 값을 다른 함수에 인자로 넘기는 상황이니 "재할당"이 불가능 한 이유도 이것으로 설명할 수 있다. 하지만 이 경우는 어떨까?

```ocaml
let x = 3 in
let y = x * 3 in
let x = y + 1 in
x + y = 19
```

위 코드에서 x는 재할당된 것으로 보인다. 정말 그럴까? Javascript로 코드를 바꿔보면 아래와 같이 나온다.

```javascript
(
  (x) =>
  ((y) =>
    ((x) => 
      x + y === 19
    )(y + 1)
  )(x * 3)
)(3)
```

무엇이 보이는가? 내부의 함수가 `x`라는 문자를 다시 종속 변수로 활용하면서, 최상위의 `x`를 덮어씌웠다. 이런 경우를 섀도잉이라 부르는데, 재할당처럼 보이지만 사실 재할당은 아니며, `x`는 외부 컨텍스트에서 여전히 존재한다.

정의가 오니 특징이 온다. 간단하지 않은가?

### 실제 세계에서의 활용

이러한 수학적 표현식이 실제 세계에서는 어떻게 활용되고 있을까? 가장 먼저, 앞서 언급했듯 ML에서 영향을 받은 모든 언어는 그 기본 문법이 위에서 표현한 `let ... in ...`으로 이루어져 있다.

조금 더 널리 쓰이는 예로는 Reactive Programming에서 흔히 볼 수 있는 pipe 함수를 들 수 있다. RxJS 공식 문서의 Operations를 보면 [Piping](https://rxjs.dev/guide/operators#piping)이란 항목이 있다. 첫 번째 Javascript 표현식을 두 번째 표현식으로 다시 쓸 수 있게 해주는 간단한 함수다.

```javascript
op4()(op3()(op2()(op1()(obs))))
```

```javascript
obs.pipe(op1(), op2(), op3(), op4());
/* 이 식은 다음과 같이 바꿀 수 있다
  obs.pipe(
    a => op1()(a)
  , b => op2()(b)
  , c => op3()(c)
  , d => op4()(d)
  )
*/
```

여기서 첫 번쨰 함수를 Javascript의 익명 함수와 `let ... in...` 표현식으로 다시 작성해보면 다음과 같다.

```javascript
((a) =>
  ((b) =>
    ((c) => 
      ((d) =>
        op4()(d)
      )(op3()(c))
    )(op2()(b))
  )(op1()(a))
)(obs)
// 😱
```

```ocaml
let a = obs in
let b = op1 () (a) in
let c = op2 () (b) in
let d = op3 () (c) in
op4 () (d)
```

pipe 함수와 수학식 사이의 연계가 보이지 않는가?

## Continuation

## 더 안전한 프로그램을 향해

### Curry-Howard-Lambek Correspondence

### 타입 추론

### 실수를 줄이자

### 부수 효과 관리

# 참고자료
- Bartosz Milewski (Aug 24, 2019). Category Theory for Programmers
- Burstall R.M, MacQueen D.B, Sannella D.T. (1980) Hope: An Experimental Applicative Language. Conference Record of the 1980 LISP Conference, Stanford University, pp. 136-143.
- J. McCarthy (Sept 1958). An Algebraic Language for the Manipulation of Symbolic Expressions. MIT AI Lab., AI Memo No. 1, Cambridge, 
- Landin, P. J. (March 1966). "The Next 700 Programming Languages". Communications of the ACM. Association for Computing Machinery. 9 (3): 157–165. doi:10.1145/365230.365257. S2CID 13409665.
- Scott Wlaschin (Jan 21 2013). [Understanding continuations](https://fsharpforfunandprofit.com/posts/computation-expressions-continuations/)
- Turner, D.A. (2013). Some History of Functional Programming Languages. In: Loidl, HW., Peña, R. (eds) Trends in Functional Programming. TFP 2012. Lecture Notes in Computer Science, vol 7829. Springer, Berlin, Heidelberg. https://doi.org/10.1007/978-3-642-40447-4_1
