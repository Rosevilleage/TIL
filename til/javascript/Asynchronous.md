> 비동기의 3가지 방식

## 비동기 (Asynchronous)
특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드들을 수행하는 방법

## 자바스크립트의 비동기 방식 3가지

### 콜백함수
```javascript
const print = (string, callback) => {
  setTimeout(function () {
    console.log(string);
    callback();
  }, Math.floor(Math.random() * 100) + 1);
};

const printAll = () => {
  print('A', () => {
    print('B', () => {
      print('C', () => {});
    });
  });
};

printAll()
// A
// B
// C
```
비동기 코드는 코드가 작성된 순서대로 작동되는 것이 아니라 동작이 완료되는 순서대로 작동하게 된다. 이는 코드 순서를 예측할 수 없다는 문제로 직결된다.

이러한 문제점을 해결하는 방법중 콜백함수의 방법으로는 콜백함수의 중첩으로 해결할 수 있다.

* 콜백함수의 중첩이 많아지면 많아질 수록 가독성이 떨어지는 콜백 지옥이 생길 수 있다. 그래서 콜백 지옥의 해결책으로 현재는 비동기 방식을 만들 때 promise나 async/await 방식을 많이 사용한다.

### 프로미스 (Promise)
promise 객체는 생성된 시점에는 알려지지 않았을 수도 있는 값을 위한 대리자다. 비동기 연산이 종료된 이후에 결과 값과 실패사유를 처리하기 위한 처리기를 연결할 수 있다.

promise를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있다. 다만 미래의 어떤 시점에 결과를 제공하겠다는 약속을 반환한다.

* promise의 세 가지 상태 (다음중 하나의 상태를 가진다)
	1. 대기 (pending) : 이행하지도, 거부하지도 않은 초기 상태
	2. 이행 (fulfilled) : 연산이 성공적으로 완료된 상태
	3. 거부 (rejected) : 연산이 실패한 상태

* peomise의 resolve, reject
	* `Promise.reject(reason)` : 주어진 사유로 거부하는 promise 객체를 반환
	* `Promise.resolve()` : 주어진 값으로 이행하는 promise객체를 반환, 이때 지정한 값이 then 가능한 (then()메서드를 가지는)값인 경우 promise.resolve()가 반환하는 프로미스는 then메서드를 따라가서 자신의 최종 상태를 결정한다. 그외에는 반환된 promise는 주어진 값으로 이행한다.

*	promise의 인스턴스 메서드 
	* `catch()` : promise에 거부 처리기 콜백을 추가하고 콜백이 호출될 경우 그 반환값으로 이행하며 호출되지 않을 경우 즉, 이전 promise가 이행하는 경우 이행한 값을 그대로 사용해 이행하는 새로운 promise를 반환
	* `then()` : promise에 이행과 거부 처리기 콜백을 추가하고, 콜백이 호출될 경우 그 반환값으로 이행하며 호출되지 않을 경우 처리된 값과 상태 그대로 처리되는 새로운 promise를 반환
	* `finally()` : promise의 이행과 거부 여부에 상관없이 처리될 경우 항상 호출되는 처리기 콜백을 추가하고, 이행한 값 그대로 이행하는 새로운 promise를 반환
		```javascript
		let promise = new Promise((resolve, reject) => resolve("success"));

		promise
		.then(value => {
			console.log(value);                              // "success"
		})
		.catch(error => {
			console.log(error);
		})
		.finally(() => {
			console.log("Executed with or without success"); // "Executed with or without success"
		})
		```
* promise 체인
	```javascript
	const url = // 가져오려는 데이터 주소
	fetch(url)
		.then((response) => response.json())
		.then((post) => console.log("post:", post)) // json 방식의 데이터 객체
		.catch((error) => console.log("error:", error));
	```
	
* promise.all()
	
	promise.all()은 순회 가능한 객체에 주어진 모든 promise가 이행된 후, 혹은 promise가 주어지지 않았을 때 이행하는 promise 객체를 반환한다. 주어진 promise 중 하나가 거부하는 경우, 첫 번째로 거절한 peomise의 이유를 사용해 자신도 거부
	```javascript
	const promise1 = Promise.resolve(3);
	const promise2 = 42;
	const promise3 = new Promise((resolve, reject) => {
		setTimeout(resolve, 100, 'foo');
	});
	Promise.all([promise1, promise2, promise3]).then((values) => {
		console.log(values);
	});
	// Expected output: Array [3, 42, "foo"]
	```
* promise의 단점
	
	peomise는 이행, 거부 등 다양한 상태에 대한 반환예약과 실제 사용시 콜백함수가 사용될 수 있다는 점에서 코드가 길어질 수록 콜백 함수와 마찬가지로 가독성이 떨어지고 거기에 더해 promise.all에서는 디버깅이 어려워 promise hell이 발생할 수 있다는 문제점이 있다.

###	async / await
async/await 를 사용하면 비동기 함수를 동기 함수 처럼 보이게 작성할 수 있다.
* `async` : async 선언은 AsyncFunction 객체를 반환하는 하나의 비동기 함수를 정의한다. 암시적으로 Promise 를 사용해 결과를 반환하지만 코드의 구문과 구조는 표준 동기 함수를 사용하는 것과 많이 비슷하다.
* `await` : await 연산자는 Promise를 기다리기 위해 사용 되며, async function 내부에서만 사용 가능하다. <br>
promise가 fulfilled되거나 reject 될 때까지 async함수의 실핼을 일시정지하고 promise가 fulfilled 되면 async함수를 일시정지한 부분부터 실행한다.<br>
이 때 await문의 반환 값은 promise에서 fulfilled된 값이다. 만약 await연산자 다음에 오는 값이 promise가 아니면 해당 값을 promise로 변환시킨다(Peomise.resolved())

```javascript
const print = (string) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve();
      console.log(string);
    }, Math.floor(Math.random() * 100) + 1);
  });
};

const printAll = async () => {
  await print('A');
  await print('B');
  await print('C');
};

printAll();
// A
// B
// C
```

___
### 참고 사이트
* 코드스테이츠 교육자료

* MDN-Asynchronous Javascript
	* https://developer.mozilla.org/ko/docs/Learn/JavaScript/Asynchronous

* MDN-await
	* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await

* MDN-async function
	* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function

* MDN-Promise.all()
	* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all

* MDN-Promise
	* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all