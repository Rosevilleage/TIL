>복사의 두 가지 종류 얕은 복사와 깊은 복사
## 복사
 자바스크립트에서 복사는 자료형에 따라 작동 방식이 조금 다르다.
* 원시 자료형

  원시 자료형은 기본적으로 변형이 불가능하다. 
	```javascript
	let a = 1;         
	let b = a;         // 여기에서 b는 a와 같은 메모리 주소(참조 값)를 가진다
	b = 2;             // b의 재할당은 원시값의 변형이 아니라 기존 주소에서 2를 가리키는 새로운 주소로의 주소(참조 값)교체다
	console.log(a, b); // b: 2, a: 1
	```

* 참조 자료형

  참조 자료형은 메모리에 저장된 값의 변경이 가능하다.
	```javascript
	let obj1 = {a:1, b:2};   
	let obj2 = obj1;          // obj2는 obj1과 같은 메모리 주소(참조 값)를 공유한다.
	obj2.b = 3;               // 메모리내에 있는 {a:1, b:2}의 값이 {a:1, b:3}으로 변형되었다.
	console.log(obj1, obj2);  // obj1: {a:1, b:3}, obj2: {a:1, b:3}
	```
	
	따라서 자바스크립트에서 복사는 해당 변수가 가리키는 값의 주소를	복사하는 것이라고 볼 수 있다.<br>
	우리가 다룰 복사의 두 가지 종류는 참조 자료형의 복사에서 이루어진다. 원시 자료형의 복사는 기본적으로 깊은 복사이다.

### 얕은 복사 (Shallow Copy)
<span style='color: orange'>얕은 복사는 원본 객체의 속성과 동일한 참조(주소 값)를 공유한다.</span>

위의 참조자료형에서 원본객체의 값이 변형된 이유가 이 때문이다. obj1과 obj2가 가지는 참조(주소)가 같고 그 참조는 교체되지 않았지만 메모리 상의 값 자체가 변형되었기 때문이다.

<p align='center'>
	<img src='image/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-01-02%20%EC%98%A4%ED%9B%84%208.31.17.png' width=650/>
</p>

* 얕은 복사 방법
1. Array.prototype.slice()

	MDN에서는 slice()를 다음과 같이 설명한다.<br>
	
		slice()는 원본을 대체하지 않습니다. 원본 배열에서 요소의 얕은 복사본을 반환합니다.
  	* 객체 참조(및 실제 객체가 아님)의 경우, slice()는 객체 참조를 새 배열로 복사합니다. 원본 배열과 새 배열은 모두 동일한 객체를 참조합니다. 참조 된 객체가 변경되면 변경 내용은 새 배열과 원래 배열 모두에서 볼 수 있습니다.
  	* String 및 Number 객체가 아닌 문자열과 숫자의 경우 slice()는 문자열과 숫자를 새 배열에 복사합니다. 한 배열에서 문자열이나 숫자를 변경해도 다른 배열에는 영향을 주지 않습니다.

	
	```javascript
	let arr1 = [{a: 1, b: 2}, false];
	let arr2 = arr1.slice();          // [{a: 1, b: 2}, false]

	arr2[0].a = 100;                  
	arr2[0].b = 200;
	arr2[1] = true;

	console.log(arr1, arr2);         // arr1 = [{a: 100, b: 200}, false] | arr2 = [{a: 100, b: 200}, true]
	```
	즉 배열안에 객체 참조가 있다면, 참조를 복사한다. 이는 2차원 배열 혹은 객체에 대한 참조를 포함하는 배열 즉, 2 레벨 깊이에서는 참조의 공유가 일어난다는 뜻이다.

2. Object.assign()

	MDN에서는 assign()을 다음과 같이 설명한다. <br>
	>Object.assign()은 속성의 값을 복사하기 때문에, 깊은 복사를 수행하려면 다른 방법을 사용해야 합니다.만약 출처 값이 객체에 대한 참조라면 참조 값만 복사합니다.

	assign()도 slice()와 마찬가지로 2 레벨 깊이에서는 참조의 공유가 일어난다.
	```javascript
	let obj1 = {a: 0, b: {c: 0}};
	let obj2 = Object.assign({}, obj1);

	obj1.a = 1;
	obj2.a = 2;
	obj2.b.c = 3;

	console.log(ojb1, obj2); // obj1 = {a: 1, b: {c: 3}} | obj2 = {a: 2, b: {c: 3}}
	```

3. spread syntax(...)

	MDN에서는 spread로 복사할 시 다음과 같은 주의를 준다.
	
	>참고: Spread 문법은 배열을 복사할 때 1 레벨 깊이에서 효과적으로 동작합니다. 그러므로, 다음 예제와 같이 다차원 배열을 복사하는것에는 적합하지 않을 수 있습니다. (Object.assign() 과 전개 구문이 동일합니다)

	spread도 위의 두 방식과 같이 2 레벨 깊이에서는 참조의 공유가 일어난다.
	```javascript
	let a = [[1], [2], [3]];
	let b = [...a];
	b.shift().shift(); // 1
	console.log(a, b); // a = [[], [2], [3]] | b = a = [[], [2], [3]]
	```

	<span style='color: orange'>**위의 세 가지 방법은 모두 2 레벨 깊이에서는 참조공유가 일어나는 얕은 복사를 한다.**</span>

### 깊은 복사 (Deep Copy)
<span style='color: orange'>깊은 복사는 원본 객체의 속성과 동일한 참조를 공유하지 않는다.</span>

원시자료형이나 1 레벨 깊이의 복사에서 위의 방법들이 원본을 변형시키지 않은 이유가 이 때문이다. <br>
참조를 공유하지 않으니 복사본이 가지는 값의 주소와 원본 값의 주소가 다른 것이다.

* 깊은 복사 방법
1. JSON.parse && JSON.stringify

	stringify()는 값이나 객체를 JSON 문자열로 변환한다. 이 과정에서 기존 참조가 끊어지게 된다.<br>
	그리고 parse()는 JSON 문자열의 구문을 분석하고, 그 결과를 자바스크립트 값이나 객체로 생성한다.
	```javascript
	let arr = ["noodles", { list: ["eggs", "flour", "water"] }];
	let arr2 = JSON.parse(JSON.stringify(arr));

	arr2[1].list = ["rice flour", "water"];

	console.log(arr[1].list); // [ "eggs", "flour", "water" ]
	```
	간단한 방법이지만 직렬화 할 수 없는 Function, Symbols, HTML DOM API에서 요소를 나타내는 개체, 재귀 데이터 등의 경우 이 방법을 사용할 수 없다. 그리고 느리다고 알려져 있다.(느린이유 찾으면 밑에 추가하겠다.)

2. 재귀 함수
	```javascript
	const object = {
		a: "str",
		obj: {
    	foo: 1,
    	bar: 2,
  	},
  	arr: [1, 2, [3, 4]],
	};
 
	function deepCopy(object) {
  	if (object === null || typeof object !== "object") {
  	  return object;
  	}
  	// 객체인지 배열인지 판단
  	const copy = Array.isArray(object) ? [] : {};
  	for (let key of Object.keys(object)) {
   	 copy[key] = deepCopy(object[key]);
  	}
	  return copy;
	}
	const copy = deepCopy(object);
 
	copy.obj.foo = 3;
	copy.arr[2].push(5);

	console.log(object); // { a: 'str', obj: { foo: 1, bar: 2 }, arr: [ 1, 2, [ 3, 4 ] ] }
	console.log(copy); // { a: 'str', obj: { foo: 3, bar: 2 }, arr: [ 1, 2, [ 3, 4, 5 ] ] }
	```
	간단하지 못하다.

3. 라이브러리 사용 (lodash)
	그나마 사용이 간단하고 범용적이라는 점이 장점으로 다가오는 방법이다.
	```javascript
	const copyMachine = require('lodash.clonedeep')

	const object = {
		a: "str",
		obj: {
    	foo: 1,
    	bar: 2,
  	},
  	arr: [1, 2, [3, 4]],
	};

	const clone = copyMachine(object);

	clone.obj.foo = 5;
	clone.arr[2].push(6);

	console.log(object); // {a: "str", obj: {foo: 1, bar: 2}, arr: [1, 2, [3, 4]]}
	console.log(clone); // {a: "str", obj: {foo: 5, bar: 2}, arr: [1, 2, [3, 4, 5]]}
	```

### 결론
javascript에서 지원하는 일반적인 메서드나 기능으로는 깊은 복사가 불가능하다.<br>
외부 라이브러리를 사용하거나 재귀함수 또는 순수한 정보를 다루는 상황에서(httpRequest로 받은 데이터)JSON을 이용하는 방법 정도가 있다.
___
 
 **참고 사이트**

 MDN-Deep copy
 * https://developer.mozilla.org/en-US/docs/Glossary/Deep_copy

 MDN-Shallow copy
 * https://developer.mozilla.org/en-US/docs/Glossary/Shallow_copy

티스토리-[JavaScript] 깊은 복사, 얕은 복사 - 뺑슨 개발 블로그
 * https://bbangson.tistory.com/78