# rxjs 소개

## 리액티브 프로그래밍이란?

사용자의 활동에 좀 더 즉각 반응하는 소프트웨어를 개발하려면 이벤트를 적절하게 처리해야한다. 그런데 프로그래밍할 때 다뤄야 할 데이터 소스의 종류가 다양해지면서 이베트 수는 점점 많아지고 있다. 즉, 요구사항은 이전과 비교했을 때 복잡하고 처리 방법은 좀 더 효율적이어야 한다는 문제를 해결해야 할 필요성이 생긴 것이다. RxJS는 방금 설명한 문제를 해결하는 방법의 하나인 리액티브 프로그래밍 패러다임이 있는 라이브러리다.

## rxjs 사용하기

rxjs를 브라우저에서 사용하는 방법 모듈 번들러를 사용할 것이다.

    npm i --save rxjs

rxjs 모듈 가져오기

```js
const { range } = require("rxjs");

range(1, 10);
```

Node.js에서 require 함수와 module.exports로 모듈을 가져오고 내보낼 때는 주로 Commonjs 프로젝트에서 제시하는 방식을 사용한다. 이 방식은 Node.js처럼 의존 모듈들이 로컬에 존재해서 매우 직관적이고 단순한 분법으로 모듈을 불러오거나 내보낼 수 있다. 하지만 브라우저에서는 의존 모듈들을 로컬에서 불러올 수 있는 상황이 아니므로 CommonJS 방식을 바로 사용하기가 어렵다. 이를 극복하기 위해 시작점이 되는 파일에서 CommonJS 방식을 사용한 모듈들을 분석한 후 하나의 자바스크립트 파일로 병합해주는 '모듈 번들러'가 등장 했다. 대표적인 라이브러리로 앞에서 소개한 브라우저리파이와 웹팩이 있다.

### webpack

먼저 덧셈을 하는 add함수를 포함한 모듈이 있는 math.js 파일을 만들자

```js
function add(a, b) {
  return a + b;
}

exports.add = add;
```

정말 단순하게 덧셈만 실행하는 add 함수가 exports 키워드로 모듈 외부에 노출되는 것을 볼 수 있다.

```js
// entrypoint.js
const math = require("./math");

console.log("1 + 2 = ", math.add(1, 2));
```

이제 웹팩을 사용해 번들링을 해보쟈.

    npm i webpack webpack-cli

rxjs 기반의 index.js

```js
const { range } = require("rxjs");
const { filter, map, scan } = require("rxjs/operators");

range(0, 10)
  .pipe(
    filter((x) => x % 2 === 0),
    map((x) => x + x),
    scan((acc, x) => acc + x, 0)
  )
  .subscribe((x) => console.log(x));
```
