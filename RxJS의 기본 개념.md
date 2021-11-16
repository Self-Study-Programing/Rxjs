# 기본 개념

이번 챕터에서는 RxJs를 이해하는 데 필요한 배경 지식과 공식 문서에서 소개하는 RxJS의 개념을 구체적으로 살펴볼 것이다.

## 배경 지식

RxJS의 기본 개념을 소개하기 전 필요한 것은 기본 개념의 바탕을 이루는 배경 지식이다. 옵저버 패턴, 명령형 프로그래밍과 함수형 프로그래밍 패러다임, 순수 함수의 핵심 등이 있다.

### 옵저버 패턴

ReactiveX 공식 문서에는 Rx의 개념이 옵저버 패턴을 확장했다고 소개한다. 공식 문서의 내용을 바탕으로 옵저버 패턴을 살펴보고, 그 이후에 ReactiveX의 옵저버블 타입이 옵저버 패턴에서 무엇을 더 확장한 것인지 알아보겠다.

옵저버 패턴의 기본 개념은 관찰하는 역할의 옵저버 객체들을 서브젝트라는 객체에 등록한 후, 서브젝트 객체의 상태 변경이 일어나면 여기에 의존성 있는 옵저버들의 메서드를 호출해서 알리는 것이다. 이를 확인하기 위해 옵저버 패턴의 UML 다이어그램을 살펴보자.

<img src="./img/uml.png" width="80%" />

그림의 Subject는 Observer를 등록하거나 해제 할 수 있다. 그리고 등록한 Subject에 어떤 변화가 일어나면 notifyObservers를 호출하여 등록된 옵저버들의 notify를 호출해 이벤트 발생을 알릴 수 있다.

### 자바스크립트 옵저버 패턴 예제

자바스크립트 개발자라면 addEventListener 및 removeEventListener 메서드로 이벤트를 전파하는 방식에 익숙할 것이라고 생각한다. 즉, 특정 이벤트를 관찰할 옵저버를 등록한 후 해당 이벤트가 발생했을 때 알려준다는 것이다. 옵저버 패턴을적용한 자바스크립트 클릭 이벤트 코드 예다.

```js
function func1() {
  console.log("target click #1");
}

function func2() {
  console.log("target click #2");
}

document.querySelector("#target").addEventListener("click", func1);
document.querySelector("#target").addEventListener("click", func2);
```

실행 결과는 다음과 같다.

    target click #1
    target click #2

#target 엘리먼트에서 클릭 이벤트가 발생했을 때 호출하는 리스너인 콜백 함수 func1과 func2를 정의했다. #target을 클릭하면 콜백 함수 각각을 호출해 'target click #1'과 'target click #2' 두 가지 메시지 모두 로그에 출력한다. 즉 func1과 func2가 옵저버로 등록되어 #target의 클릭 이벤트를 관찰하다가 이벤트가 발생하면 리스너가 호출되는 것이다. 이렇게 콜백 함수가 동작하는 상황을 '옵저버가 이벤트를 구독한다'라고 한다.

### 함수형 프로그래밍과 순수 함수

명령형 프로그래밍은 코드의 흐름에 따라서 조건문 반복문 등으로 분기하고 각각의 흐름대로 차례차례 코드를 작성하는 방식이다. 자바스크립트는 멀티 패러다임 언어라 명령형 프로그래밍과 함수형 프로그래밍을 모두 지원하지만 많은 개발자가 명령형 프로그래밍에 익숙하다고 생각한다. 명령형 방식에 익숙한 자바스크립트 개발자라면 코루틴 기반의 co 라이브러리나 이를 추상화한 async/await 방식이 여러 값들을 비동기 처리할 때 좀 더 편할 것이다.

하지만 RxJS는 비동기로 처리하는 여러 값을 명령형 프로그래밍이 아닌 함수형 프로그래밍 패러다임으로 다룬다,

함수형 프로그래밍의 주요 특징 중 하나는 함수를 값으로 취급하는 1급이라는 특성이다. 함수를 인자로 사용하거나 함수 자체를 리턴하거나 함수를 변수에 할당할 수 있다. 이 때문에 함수의 합성이 가능하고, 여러 함수를 미리 합성한 후 필요한 시점에 해당 함수를 호출할 수 있다.

보통 함수형 프로그래밍에서 다루는 함수는 수학적인 정의의 함수처럼 주어진 값에 따른 고정된 결과를 계산하려는 것이다. 입출력 동작이나 외부 변수 참조 처럼 수학적 정의의 함수 동작과 상관없는 부수 효과가 없다. 그래서 함수형 프로그래밍에서 다루는 함수는 입력 데이터에 관한 출력 데이터가 항상 같아야 한다. 이러한 특성 때문에 함수형 프로그래밍의 함수는 다른 프로그래밍의 함수와 구분하려고 순수 함수라고도 한다. 순수 함수는 함수의 결과각 상상 보장되므로 잘 사용하면 값을 안전하게 관리하고 디버깅하기 쉽다.

### 옵저버블

ReactiveX 공식 문서 마지막에는 옵저버블 타입에 GoF의 옵저버 패턴에 없는 두 가지 의미가 추가되었다고설명한다. 하나는 더 이상 데이터가 없음을 알리는 onCompleted 메서드(RxJS에서는 complete 함수)고, 다른 하나는 에러가 발생했음을 알리는 onError 메서드(RxJS에서는 error 함수)다. 즉, 옵저버 패턴을 기반으로 방금 소개한 두 가지 개념을 추가한 것이 옵저버블이다.

두 가지 개념은 이터러블 타입도 소개한다. 이터러블이 forEach 연산자를 반복 살행한 후 완료되는 것이 onCompleted, 반복 실행하는 동안 이터러블에서 에러가 발생했을 때를 onError로 표현한다.

이는 뒤에서 소개할 이터러블/이터레이터 요소를 옵저버블/옵저버로 뒤집어 놓은 것이다.

RxJS의 핵심 구성 요소인 옵저버블, 구독, 연산자, 서브젝트, 스케줄러 등

RxJS는 옵저버 패턴을 적용한 옵저버블이라는 객체를 중심으로 동작한다. 옵저버블은 특정 객체를 관찰하는 옵저버에게 여러 이벤트나 값을 보내는 역할을 한다. 좀 더 구체적으로 설명하면 옵저버블 객체안에서 여러 개의 값이나 이벤트를 취급하고 옵저버의 함수를 호출해 필요하 값이나 이벤트를 보내는 방식이다.

RxJS 공식 문서의 옵저버블 설명에 따르면 하나의 값이나 이벤트를 다루는 싱글, 여러 개의 값이나 이벤트를 다루는 멀티플, 데이터를 받을지 결정하는 풀, 데이터를 보낼지 결정하는 푸시라는 네 가지 개념이 있다. 그리고 아래에 함수, 프로미스, 이터레이터, 옵저버블의 개념을 구분한다.

|      | 싱글     | 멀티플     |
| ---- | -------- | ---------- |
| 풀   | 함수     | 이터레이터 |
| 푸시 | 프로미스 | 옵저버블   |

또한 옵저버블은 데이터를 만드는 생산자와 데이터를 사용하는 소비자의 관계로도 설명할 수 있다. 함수, 이터레이터, 프로미스, 옵저버블은 값을 만들어내는 생산자 역할을 한다. 이를 가져다 사용하는 입장인 function.call iterator.next, promise.then, 옵저버블과 연결된 옵저버는 소비자 입장이다. 데이터를 소배하는 쪽이 능동적으로 데이터를 호출하고 데이터를 생산하는 쪽은 데이터를 소비하는 쪽의 영향을 받는다.

하지만 옵저버블이 속해 있는 푸시 방식은 데이터를 생산하는 생산자가 주체다. 즉, 이벤트나 값 같은 데이터를 생산하는 쪽에서 준비가 되면 데이터를 소비하는 소비자에게 알려주는 방식이다. 그래서 프로미스든 옵저버블이든 생산자가 능동적으로 데이터를 생산하면 알림을 받을 수 있는 콜백이 있다.

## 옵저버블의 라이프 사이클

RxJS 공식 문서에는 옵저버블의 라이프사이클을 다음과 같이 설명한다.

1. 옵저버블 생성(Creating Observables)
2. 옵저버블 구독(Subscribing to Observables)
3. 옵저버블 실행(Executing the Observable)
4. 옵저버블 구독 해제(Disposing Observables)

시작은 옵저버블의 생성이다. 보통 require('rxjs')에서 불러온 Observable 클래스의 정적 함수 Observable.create로 직접 옵저버블을 생성한다. 그런데 Observable 클래스의 정적 함수가 아닌 require('rxjs')에서 불러올 수 있는 함수 중 range나 of로도 필요한 옵저버블을 쉽게 생성할 수 있다. 이 방식은 추상화된 형태로 옵저버블을 사용한다는 장점이 있다. 또한 이렇게 생성된 옵저버블 인스턴스에 Observable.prototype으로 연결된 pipe 함수에 다양한 연산자를 인자로 사용해서 새로운 옵저버블 인스턴스를 생성할 수 있다.

구독과 실행은 데이터를 전달할 콜백을 제공해 함수를 호출(구독)한 후 옵저버블에서 발행하는 값을 사용하는 것으로 설명할 수 있다. subscribe 함수를 이용한다. 함수를 여러 번 호출해도 해당 함수가 각각 독립적으로 동작한다는 특징이 있다. 옵저버가 구독하는 이벤트에 이벤트가 발생하면 모든 옵저버가 같은 결과를 전달받도록 여러 옵저버에 멀티캐스팅했다는 뜻이다.

옵저버블을 구독해 실행하는 자바스크립트 이벤트 처리

```js
const { Observable } = require("rxjs");

const observableCreated$ = Observable.create(function (observer) {
  for (let i = 1; i <= 10; i++) {
    setTimeout(function () {
      observer.next(i);
      if (i === 10) {
        observer.complete();
      }
    }, 300 * i);
  }
});

observableCreated$.subscribe(
  function next(item) {
    console.log(`observerA: ${item}`);
  },
  function error(err) {
    console.log(`observerA: ${err}`);
  },
  function complete() {
    console.log(`observerA: complete`);
  }
);

setTimeout(function () {
  observableCreated$.subscribe(
    function next(item) {
      console.log(`observerB: ${item}`);
    },
    function error(err) {
      console.log(`observerB: ${err}`);
    },
    function complete() {
      console.log(`observerB: complete`);
    }
  );
}, 1350);
```

setTimeout 함수를 이용해 일정 간격으로 값을 발행하는 옵저버블이 있고 해당 옵저버블을 observerA란 이름으로 먼저 구독한 후, 1350ms 후에 observerB란 이름으로 같은 옵저버블을 구독한다. observerA와 observerB가 같은 값을 발행하지 않고 observerB를 구독한 시점부터는 1부터 새로운 값을 발행한다.

같은 값을 발행할 수 있도록 멀티캐스팅하지 않는 상황이다. 즉, 뒤늦게 구독해도 클릭 이벤트가 발생하면 같은 내용을 전달받아야 하는데 그렇지 않다. RxJS의 옵저버블은 멀티캐스팅이 안 될 때와 될 때를 모두 지원한다.

구독 해제는 쉽게 말해 옵저버블의 구독을 해제하는 것이다. 예외 상황이 있긴 하지만 옵저버블에서 발행하는 값을 더 받지 않는다. unsubscribe 함수를 사용해 구독 해제를 알리거나 구독 해제 후 해야 할 처리를 정의한다.

참고로 명령형 프로그래밍에 익숙하다면 RxJS를 사용할 때 주의할 점이 있다. 각 연산자에 넘겨준 함수에 return이나 break를 설정한다고 옵저버블의 동작이 중단되지 않는다는 것이다. RxJS에서 실행되는 함수에서 return이나 break를 사용하면 해당 함수 안에서만 실행을 중단한다. 해당 옵저버블 구독을 중단한다는 의미가 아니다.

### 옵저버블 생성하고 실행하기

Observable.create라는 함수를 호출해 옵저버블을 생성하고, 옵저버블 인스턴스에 있는 subscribe 함수를 호출해 옵저버블을 구독하고 실행하는 예다.

```js
const { Observable } = require("rxjs");

const observableCreated$ = Observable.create(function (observer) {
  console.log("BEGIN Observable");
  observer.next(1);
  observer.next(2);
  observer.complete();
  console.log("END Observable");
});

observableCreated$.subscribe(
  function next(item) {
    console.log(item);
  },
  function error(e) {},
  function complete() {
    console.log("complete");
  }
);
```

실행결과

    BEGIN Observable
    1
    2
    complete
    END Observable

subscribe 함수의 호출 부분을 주석 처리하면 아무일도 일어 나지 않는다. 즉, 생성한 옵저버블은 옵저버에게 값을 전달하는 함수가 있지만 subscribe 함수가 호출되어야 옵저버블과 옵저버를 연결해 실행한다. 옵저버의 구성 요소와 옵저버블과 옵저버의 관계를 표현하면 다음과 같다.

옵저버블 객체 생성 자체는 아무 일도 하지 않고 어떤 일을 해야 할지에 관한 정보만 있고, subscribe 함수를 호출해야 옵저버블이 옵저버에 데이터를 전달하며 동작을 실행한다. 옵저버 객체는 next, error, complete라는 세 가지 함수로 구성되어 있고, subscribe 함수에는 next, error, complete 함수 순서로 옵저버의 구성 요소나 콜백 함수들을 객체로 감싼 옵저버 객체를 전달할 수 있다.

next, complete

```js
Observable.create(function (observer) {
  console.log("BEGIN Observable");
  observer.next(1);
  observer.next(2);
  observer.complete();
  observer.next(3);
  console.log("END Observable");
}).subscribe(
  function next(item) {
    console.log(item);
  },
  function error(e) {},
  function complete() {
    console.log("complete");
  }
);
```

    BEGIN Observable
    1
    2
    complete
    END Observable

옵저버블 객체에서 subscribe 함수를 호출하면 옵저버블이 옵저버의 complete나 error 함수를 호출할 때까지 next 함수로 값을 발생한다. observer.next(3)까지 호출할 수 있지만 next(3)을 호출하기 전 옵저버의 complete 함수를 호출했으므로 subscribe 함수 안에 있는 next 함수에 값 3은 발행되지 않는다.

### 구독 객체 관리하기

옵저버는 앞에서 언급한 next, error, complete라는 세 가지 함수로 구성된 객체다. 옵저버블은 각 연산자를 거쳐 subscribe 함수 안 옵저버로 값을 전달한다. 즉, subscribe 안 함수 각각을 사용해 옵저버 객체를 생성하고 이 옵저버 객체로 함수 각각을 호출해 값을 발행한다.

구독을 멈추게 하는 함수는 unsubscribe다. subscribe가 리턴하는 객체는 Subscription 클래스의 타입이다. Subscription 타입은 unsubscribe 함수를 호출해야 구독을 멈추게 할 수 있다. unsubscribe 함수를 호출하면 특정 시점에 호출해야 하는 옵저버블 내부의 함수를 호출하며, 해당 함수 안에서는 관련 자원을 구독 해제한다.

다음 예제는 setInterval 메서드로 1초마다 'hi'라는 문자열을 보내고 옵저버블을 구독을 해제하는 용도로 unsubscribe 함수를 리턴한다.

```js
const { Observable } = require("rxjs");

const observableCreated$ = Observable.create(function subscribe(observer) {
  const intervalID = setInterval(function () {
    observer.next("hi");
  }, 1000);

  return function unsubscribe() {
    clearInterval(intervalID);
  };
});
```

옵저버블을 생성할 때는 리턴 함수로 unscribe를 제공한다. 그리고 이 함수를 호출했을 때는 clearInterval(intervalID) 메서드를 호출해서 1추마다 'hi'를 보내는 동작을 중단한다. 만약 create 함수 안에서 메모리나 자원을 사용하면서 이를 해제하는 unsubscribe 함수를 리턴하지 않으면 옵저버블이 자원을 제대로 해제할 수 없다. 이러면 Subscription의 구독을 해제하려고 unsubscribe 함수를 호출하거나, 내부에서 complete 함수나 error 함수를 호출한다.

rxjs에서 interval 함수는 0부터 1씩 증가하는 정숫값을 계속 반환한다.

```js
const { interval } = require("rxjs");
const observable = interval(1000);

const subscription = observable.subscribe(function (x) {
  console.log(x);
});

subscription.unsubscribe();
```

구독하자마자 unsubscribe 함수를 호출하므로 화면에 아무것도 출력되지 않는다. interval 함수는 비동기 방식으로 동작하는데, 자바스크립트에서는 동기 방식의 동작이 끝난 후 큐에 있는 비동기 작업을 실행하기 때문이다. 즉, 동기 방식에서 unsubscribe 함수로 구독을 해제해서 1초마다 하는 작업을 큐에서 제거한다. 따라서 이후에 아무 일도 일어나지 않는다.

```js
const { interval } = require("rxjs");
const observable1 = interval(400);
const observable2 = interval(300);

const subscription = observable1.subscribe(function (x) {
  console.log("first: " + x);
});

const childSubscription = observable2.subscribe(function (x) {
  console.log("second: " + x);
});

subscription.add(childSubscription);

setTimeout(function () {
  subscription.unsubscribe();
}, 4000);
```

여기에서는 400ms, 300ms마다 값을 발행하는 2개 옵저버블의 subscribe 함수를 호출하여 구독한다. 처음 구독한 결과는 subscription 변수에 할당하고, 그 다음 구독하는 결과는 childSubscription 변수에 할당한다. subscription.add로 childSubscription 객체를 추가했고, unsubscribe 함수를 호출해서 모든 Subscription 인스턴스의 구독을 해제했다. add나 remove 함수를 사용할 때는 해당 Subscription 객체가 어떤 Subscription 객체까지 영향을 주는지 항상 주의해서 사용해야 한다.

## 서브젝트

서브젝트는 멀티캐스팅을 지원하는 객체다. 멀티캐스팅을 지원한다는 것은 여러 옵저버가 이벤트 변경이나 값 전달을 관찰하도록 옵저버블을 구독한 후, 실제 이벤트 변경이나 값 전달이 발생했을 때 이를 알린다는 뜻이다. 구독 중인 모든 옵저버가 호출되어 같은 값을 전달받는다는 뜻이다. 즉, subscribe 함수로 여러 옵저버를 등록한 후 next 함수로 발행하는 값을 여러 옵저버에 전달할 수 있다.

서브젝트는 옵저버블이면서 옵저버 역할도 한다. 즉, 옵저버블이므로 여러 옵저버가 옵저버블을 구독할 수 있고, 옵저버이기도 하므로 next, error, complete 함수를 호출해 같은 결과를 전달 받을 수 있다.

다음은 서브젝트에 있는 기본 동작을 살펴볼 수 있는 예다.

```js
const { Subject } = require("rxjs");

const subject = new Subject();

subject.subscribe({
  next: function (v) {
    console.log("observerA: " + v);
  },
});

subject.subscribe({
  next: function (v) {
    console.log("observerB: " + v);
  },
});

subject.next(1);
subject.next(2);

// subject.complete();
// subject.next(3);
```

## 연산자

RxJS의 연산자는 기본적으로 함수 형태다. 즉 map이나 filter와 같은 여러 값ㅇ르 취급할 수 있는 연산자를 제공한다. 연산자의 일부는 개발자가 작성한 함수를 인자로 사용해 동작하는 것도 있다. 예를 들어 map 연산자는 값을 어떻게 변환할지 정하는 함수를 인자로 만들어 사용한다.

연산자를 사용하려면 옵저버블을 생성해야 한다. 따라서 옵저버블을 생성하는 함수나 이미 생성된 옵저버블 인스턴스에 pipe 함수로 값을 다룰 연산자들이 필요하다. 예를 들어 Observable.create는 옵저버블을 생성하는 일반적인 생성 함수다. rxjs에서 불러온 생성 함수로는 1개의 값만 발행하는 옵저버블을 생성하는 of, 특정 범위의 값을 순서대로 발행하는 옵저버블을 생성하는 range 등이 있다.

생성 함수인 interval과 파이퍼블 연산자인 filter를 각각 rxjs와 rxjs/operators에서 불러와서 pipe 함수 안에서 사용한다.

```js
const { interval } = require("rxjs");
const { filter } = require("rxjs/operators");

let divisor = 2;
setInterval(function () {
  divisor = (divisor + 1) % 10;
}, 500);

interval(700)
  .pipe(
    filter(function (value) {
      return value % divisor == 0;
    })
  )
  .subscribe((value) => console.log(value));
```

### RxJS 5와 6의 연산자 차이

RxJS 5의 공식 문서에서는 옵저버블을 생성해서 출발점이 될 수 있는 정적 연산자와 생성된 옵저버블 인스턴스에서 호출할 수 있는 인스턴스 연산자를 나누었다. RxJS 6에서는 이런 개념이 사라졌다. 따라서 정적 연산자처럼 옵저버블을 생성하는 함수를 정의해야 한다.
