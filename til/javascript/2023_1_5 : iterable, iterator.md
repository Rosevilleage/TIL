>반복가능한 객체 iterable과 그 속성인 iterator

## 이터러블 (iterable)
 >이터러블은 반복이 가능한 객체를 뜻하며 이는 `for..of` 혹은 그와 같은 방식에서 사용 가능함을 의미한다.
  * 이터러블은 `@@iterator` 메서드를 가지며 `for..of`는 `@@iterator` 메서드를 호출해 객체를 반복한다. 즉, 객체 자신이나 프로토파입 체인(prototype chain)의 객체중 하나가 `[Symbol.iterator]` `key` 의 속성을 가져야 한다.
 
 ### iterable 예시
  * 내장 iterables : `String`, `Array`, `TyprArray` 이 객체들의 프로토타입 객체들은 모두 `@@iterator` 메서드를 가지고 있다.
  ```javascript
  let str = 'abcdef';
  typeof str[Symbol.iterator]; // 'function'

  let arr = [1, 2, 3];
  typeof arr[Symbol.iterator]; // 'function'
  
  for(const word of str) {console.log(word)} 
  // 'a'
  // 'b'
  // 'c'
  // 'd'
  // 'e'
  // 'f'
  for(const el of arr) {console.log(el)} 
  // 1
  // 2
  // 3
  ```
  * iterable을 허용하는 API : `Map([iterator])`, `WeakMap([iterator])`, `Set([iterator])`, `WeakSet([iterator])`, `Promise.all(iterator)`, `Promise.race(iterator)`, `Array.from()`
  ```javascript
  var myObj = {};
  let map = new Map([[1,"a"],[2,"b"],[3,"c"]]).get(2);               // "b"
  typeof map[Symbol.iterator];                                       // 'function'
  
  let weakMap = new WeakMap([[{},"a"],[myObj,"b"],[{},"c"]]).get(myObj); // "b"
  typeof weakMap[Symbol.iterator];                                   // 'function'
  
  let set = new Set([1, 2, 3]).has(3);                               // true
  typeof set[Symbol.iterator];                                       // 'function'
  
  let weakSet = new Set("123").has("2");                             // true
  typeof weakSet[Symbol.iterator];                                   // 'function'
  
  let weakSet2 = new WeakSet(function*() {
    yield {};
    yield myObj;
    yield {};
  }()).has(myObj);                                                    // true
  typeof weakSet2[Symbol.iterator];                                   // 'function'
  ```
  * iterable과 함께 사용되는 문법 : `for..of`, `spread operator`, `yield*`, `destructuring assignment`
  ```javascript
  for(let value of ["a", "b", "c"]){
    console.log(value);
  }
  // "a"
  // "b"
  // "c"

  [..."abc"]; // ["a", "b", "c"]

  function* gen(){
    yield* ["a", "b", "c"];
  }

  gen().next(); // { value:"a", done:false }

  [a, b, c] = new Set(["a", "b", "c"]);
  a // "a"
  ```
  
## 이터레이터 (iterator) 
 >`next()` 메서드를 가지는 객체
 
 * `next()` : 아래의 속성들을 가진 object를 반환하는 arguments가 없는 함수
   * `done` (boolean)
     * iterator가 마지막 반복을 마쳤을 경우 `true`. 만약 iterator에 return값이 있다면 value의 값으로 지정
     * iterator의 작업이 남아있다면 `false`. iterator에 `done` 값을 지정하지 않는 것과 같다.
   * `value` : iterator로부터 반환되는 모든 자바스크립트 값, `done`이 true일 경우 생략 가능  
   
 * `Symbol.iterator` : object를 반환하는 arguments가 없는 함수, the iterator peotocol 에 따라 구현
 ```javascript
  let numObj = {
    from: 1,
    to: 5
  };

  // for..of 최초 호출 시, Symbol.iterator가 호출
  numObj[Symbol.iterator] = function() {
    // Symbol.iterator는 이터레이터 객체를 반환
  
    // 이후 for..of는 반환된 이터레이터 객체만을 대상으로 동작, 이때 다음 값도 정해진다.
    return {
      current: this.from,
      last: this.to,

      // for..of 반복문에 의해 반복마다 next()가 호출
      next() {
        // next()는 값을 객체 {done:.., value :...}형태로 반환
        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  };

  for (let num of numObj) {
    alert(num); // 1, then 2, 3, 4, 5
  }
 ```
 
## 이터러블과 유사배열
 >반복 가능하고 배열 자체가 아니라는 점에서 유사하지만 구조적으로 차이를 가진다.
 
 * 이터러블(iterable) : `Symbol.iterator` 즉 `@@iterator` 메서드가 구현된 객체
 * 유사 배열(Array-like) : `index`와 `length` 프로퍼티가 있어 배열처럼 보이는 객체
 
 둘은 `for..of` 혹은 그와 같은 방식에서 사용할 수 있는지 여부가 다르다. 유사배열은 `for..of` 를 사용할 수 없다.
 
## 결론
 * 이터러블엔 메서드 `@@iterator`(Symbol.iterator)가 구현되어 있어야 한다.
   * `obj[Symbol.iterator]`의 결과를 이터레이터라고 부르며, 이터레이터는 이어지는 반복 과정을 처리한다.
   * 이터레이터엔 객체 `{done: Boolean, value: any}`을 반환하는 메서드 `next()`가 구현되어 있어야 한다.
 * 메서드 `Symbol.iterator`는 `for..of`에 의해 자동으로 호출된다. 개발자가 명시적으로 호출하는 것도 가능하다.
 * 문자열이나 배열 같은 내장 이터러블에도 `Symbol.iterato`가 구현되어 있다.

 * 인덱스와 length 프로퍼티가 있는 객체는 유사 배열이라 불린다. 유사 배열 객체엔 다양한 프로퍼티와 메서드가 있을 수 있지만 배열 내장 메서드의 경우에는 없다.

___
**참고 사이트**
 * https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Iteration_protocols
 * https://ko.javascript.info/iterable