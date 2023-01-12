>커링과 클로저의 차이점에 주목하며 정리해본다.

## 커링 (Currying)
커링은 함수와 함께 사용할 수 있는 고급기술로 `func(a, b, c)` 처럼 단일 호출로 처리하는 함수를 `func(a)(b)(c)` 처럼 각각의 인수가 호출 가능한 프로세스로 호출된 후 병합되도록 변환하는 것이다.
```javascript
function curry(f) { // 커링 변환을 하는 curry(f) 함수
  return function(a) {
    return function(b) {
      return f(a, b);
    };
  };
}

function sum(a, b) {
  return a + b;
}

let curriedSum = curry(sum);

alert( curriedSum(1)(2) ); // 3
```

위의 예시에서 보이듯, 그저 함수를 중첩해 반환할뿐이다.

* curry(func)의 반환값은 function(a)형태의 래퍼이고,
* curriedSum(1)가 호출되었을 때, 그 인수는 렉시컬 환경에 저장이 되고 새로운 래퍼 function(b)가 반환된다.
* 그리고 반환된 래퍼 function(b)가 2를 인수로 호출,  반환값이 원래의 sum으로 넘겨져서 호출된다.

고급 커링 구현

고급 커링은 함수를 기존의 단일 호출을 포함한 각각의 인수가 호출 가능한 프로세스 모두를 가능하게한다.
```javascript
function curry(func) {
	
	return function curried(...args) {
	  if (args.length >= func.length) { // (1)
	    return func.apply(this, args);
	  } else {
	    return function pass(...args2) { // (2)
	      return curried.apply(this, args.concat(args2));
	    }
	  }
	};
}	

function sum(a, b, c) {  //  (3)
  return a + b + c;
}

let curriedSum = curry(sum);
let curriedHold1 = curriedSum(1); // 하나의 인수 고정

console.log(curriedSum(1, 2, 3)); // 6, 보통때 처럼 단일 callable 형식으로 호출
console.log(curriedSum(1)(2,3)); // 6, 첫 번째 인수를 커링
console.log(curriedSum(1)(2)(3)); // 6, 모두 커링
console.log(curriedHold1(2, 3)); // 6, 고정후 커링
```

* 예시에서 `args.length` 는 호출에서 curriedSum이 받을 인수의 개수를 뜻한다. 
* `func.length` 는 (3)에서 sum이 필요로하는 인수의 개수를 뜻한다.
* (1)에서 curriedSum이 받아온 인수의 개수가 func.length보다 작으면 curried는 pass를 반환한다.
* pass는 curried의 다음 인수에 처음받은 인수를 병합한다.
* 다시 조건문이 시작되고, 조건을 만족할때 까지 반복한다. 그리고 최종적으로 모든 인수가 병합된 `(1, 2, 3)`이 최종함수에 전달된다.
* 또한 하나의 인수를 미리 고정해놓고 나머지 인수를 전달하는 것도 가능하다.

## 클로저 (closure)
클로저는 함수와 함수가 선언된 어휘적 환경 혹은 함수를 반환하는 함수라도고 소개된다. 
 * 특징
 1. 함수를 리턴하는 함수의 형태로 많이 보인다.
 2. 내부함수에서 외부변수에 접근하능하고 [[Enviroment]]를 통해 외부 함수 내의 렉시컬 환경을 저장해 놓을 수 있다.
 3. 인자를 미리 고정해 사용도 가능하다.

 ```javascript
  function makeAdder(x) {
    let y = 1;
    return function(z) {
      y = 100;
      return x + y + z;
    };
  }
  let add5 = makeAdder(5);
  let add10 = makeAdder(10);
  //클로저에 x와 y의 환경이 저장됨
  console.log(add5(2));  // 107 (x:5 + y:100 + z:2)
  console.log(add10(2)); // 112 (x:10 + y:100 + z:2)
  //함수 실행 시 클로저에 저장된 x, y값에 접근하여 값을 계산
 ```

두 설명을 읽었을 때 클로저는 커링과 많은 유사점을 보이는데 이는 커링이 클로저의 특성을 활용한 기술이기 때문이다.

즉, 커링은 클로저가 [[Enviroment]]에 외부 환경을 저장 하는 특성을 활용하여, 함수 호출 프로세스를 변환한다고 볼 수 있다.

___

**참고 사이트**
MDN-클로저
* https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures

JAVASCRIPT.INPO-커링
* https://ko.javascript.info/currying-partials