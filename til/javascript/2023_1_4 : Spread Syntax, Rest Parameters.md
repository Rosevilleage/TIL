## 전개 구문(Spread Syntax)
  >전개구문은 배열이나 문자열과 같이 반복가능한 문자를 0개 이상의 인수(함수로 호출) 또는 요소(배열의 리터럴)로 확장하여, 0개 이상의 키-값 쌍의 객체로 확장시킬 수 있다.
 
  ### 함수 호출에서의 전개
  * 일반적으로 배열의 엘리먼트를 함수의 인수로 사용하고자 하면, `.apply()`를 사용하지만 전개구문(`...`)으로 간결하게 사용 가능하다.
  ```javascript
  //aplly
  function useApply(a, b, c) {}
  let args = [0, 1, 2];
  useApply.apply(null, args);
 
  //spread
  function useSpread(a, b, c) {}
  let args = [0, 1, 2];
  useSpread(...args);

  //spread2
  function useSpread(a, b, c, d, e) {}
  let args = [0, 1];
  useSpread(-1, ...args, 2, ...[3]);
  ```
 
  * `new` 를 사용해 생성자를 호출할 때 배열과 apply를 직접 사용하는 것이 불가능 했으나, 전개구문을 사용해 배열을 new와 함께사용하는 것이 가능해졌다.
  ```javascript
  let date = [2022, 2, 2];
  let d = new Date(...date); // Wed Mar 02 2022 00:00:00 GMT+0900 (한국 표준시)
  ```
 
 ### 배열 리터럴에서의 전개
   spread는 배열 리터럴의 어디에서든지 사용될 수 있고, 여러번 사용될수도 있다.
 
  * 배열 복사
  ```javascript
  let arr = [1, 2, 3];
  let arr2 = [...arr];
  arr2.push(4); // arr = [1, 2, 3], arr2 = [1, 2, 3, 4]
  // spread는 배열을 복사할 때 레벨 1 깊이에서 효과적, 다차원 배열에는 부적합할수 있음(assign()과 동일)
  let a = [[1], [2], [3]];
  let b = [...a];
  b.shift().shift(); // a = [[], [2], [3]]
  ```
 
  * 배열의 연결 
    * 기존의 연결은 `.concat()` 을 사용했다.
    ```javascript
    //concat
    let arr1 = [1, 2, 3];
    let arr2 = [4, 5, 6];
    arr1 = arr1.concat(arr2);
    //spread
    let arr1 = [1, 2, 3];
    let arr2 = [4, 5, 6];
    arr1 = [...arr1, arr2];
    ```
    * 배열의 시작지점에 값들을 삽입할때는 `unshift()`를 사용했디
    ```javascript
    // unshift
    let arr1 = [1, 2, 3];
    let arr2 = [4, 5, 6];
    Array.prototype.unshift.apply(arr1, arr2) // arr1 = [3, 4, 5, 1, 2, 3]
    //spread
    let arr1 = [1, 2, 3];
    let arr2 = [4, 5, 6];
    arr1 = [...arr2, ...arr1]; // arr1 = [3, 4, 5, 1, 2, 3]
    ```
   
 ### 객체 리터럴에서의 전개
   제공된 객체가 소유한 열거형 프로퍼티를 새로운 객체로 복사한다.
  * 얕은 복제(prototype 제외) 또는 객체의 병합은 `.assign()` 보다 짧게 사용할 수 있다.
    ```javascript
    let obj1 = {name: 'john', age: 35};
    let obj2 = {name: 'joon', hrught: 180};
    let cloneObj = {...obj1};
    let let mergeObj = {...obj1, ...onj2}; // Object{name: 'joon', age: 35, height: 180}
    ```
  * 그러나 spread`...` 는 `assign()` 과 달리 setters를 트리거 하지않아 `spread`가 `assign`을 대체할수는 없다.
    ```javascript
    let obj1 = {name: 'john', age: 35};
    let obj2 = {name: 'joon', hrught: 180};
    const merge = (...objects) => ({...objects});
    let mergeObj = merge(obj1, obj2); // Object{0: {name: 'john', age: 35}, 1: {name: 'joon', hrught: 180}}
    let mergeObj = merge({}, obj1, obj2); // Object{0: {}, 1: {name: 'john', age: 35}, 2: {name: 'joon', hrught: 180}}
    ```

 ### 이터러블 전용
  * 전개 구문(spread 프로퍼티인 경우 제외)은 iterable 객체에만 적용된다. 
    +예외) 이터러블이 아닌 객체도 ECMAScript의 Rest/Spread 프로퍼티 제안(stage 4)으로 인해 객체 리터럴에 속성 전개가 가능해졌다.
  * spread는 `for..of`와 같은 방식으로 내부에서 이터레이터(iterator, 반복자)를 사용해 요소를 수집한다. 문자열에 사용한다면, `for..of` 는 문자열을 구성하는 문자를 반환하고 spread도 문자를 반환하는데 이 문자 목록은 배열 초기자(array initializer)`[...str]`로 전달된다.
    ```javascript
    let str = 'hello';
    console.log([...str]) // h, e, l, l, o
    ```
 
## 나머지 매개변수(Rest Parameters)
>나머지 매개변수는 함수가 정해지지 않은 수의 매개변수를 배열로 받을 수 있게 한다.
 * 함수의 마지막 매개변수 앞에 `...` 을 붙이면 (사용자가 제공한) 모든 후속 매개변수를 배열로 만든다. 마지막 매개변수만 나머지 매개변수로 설정할 수 있다.(`...`은 마지막 매개변수에만 쓸수 있다.)

 ### 나머지 매개변수와 arguments객체의 차이
   1. `arguments` 객체는 실제 배열이 아니지만 rest`...`는 `Array`인스턴스이므로 배열 메서드를 직접 적용할 수 있다.
   2. `arguments` 객체는 `callee` 속성과 같은 추가 기능을 포함한다.
   3. rest는 후속 매개변수만 배열에 포함하므로 rest이전에 직접 정의한 매개변수는 포함하지 않지만 `arguments` 객체는 rest의 각 항목까지 더해 모든 매개변수를 포함한다.
   
   * 나머지 매개변수와 일반 매개변수 함께 사용
   ```javascript
   function multiply(num, ...arr) {
     return arr.map(el=>num*el) // 나머지 매개변수로 만든 배열의 요소들을 일반 매개변수와 곱한다.
   }
   let result = miltiply(2, 10, 15, 36);
   console.log(reslut); // [20, 30, 72]
   ```
   
   * arguments는 유사배열
   ```javascript
   function sortRestArgs(...restParams) {
     let sortedArgs = restParams.sort()
     return sortedArgs
   }
   console.log(sortRestArgs(5, 4, 2, 1)); // 1, 2, 4, 5

   function sortArguments() {
     let sortedArgs = arguments.sort()
     return sortedArgs
   }
   console.log(sortArguments(5, 4, 2, 1)); //Uncaught TypeError: arguments.sort is not a function

   //arguments 객체에서 배열 매서드를 사용하려면 실제 배열로 변환해야 한다.
   function sortArguments() {
     let arrAgrs = Array.from(arguments)
     let sortedArgs = arguments.sort()
     return sortedArgs
   }
   console.log(sortArguments(5, 4, 2, 1)); // 1, 2, 4, 5
   ```
    * arguments는 이터러블 객체(배열도 이터러블 객체데 속한다)가 아닌 유사배열에 속한다.  
      * 이러터블 객체는 나중에 정리하겠지만 반복이 가능한 for..of를 사용할 수 있는 객체를 뜻하는 듯하다.
    * arguments는 이터레이터(iterator)를 가지지 않고, 인덱스와 length 프로퍼티가 있어서 배열처럼 보이는 객체이다.
## 전개 구문과 나머지 매개변수의 차이
>전개 구문과 나머지 매개변수는 정확이 같아 보이지만(둘 다 `...`), 나머지 매개변수는 전개 구문과 반대되는 역할을 가진다.
전개 구문은 배열의 그 엘리먼트로 확장하는 반면, 나머지 매개변수는 여러 엘리먼트를 수집하여 하나의 엘리먼트로 압축한다.

* 함수 매개변수의 마지막에 존재하며, 인수 목록의 나머지를 모아 배열로 모으는 `...` = 나머지 매개변수(Rest Parameters)
  * 인수 개수에 제한이 없는 함수를 만들 때 나머지 매개변수를 사용

* 함수 호출 시 사용되거나 그 외의 경우에 배열을 목록으로 확장하는 `...` = 전개 구문(Spread Syntax)
  * 다수의 인수를 받는 함수에 배열을 전달할 때 전개 구문을 사용

> 똑같이 생겨서 상당히 헷갈렸다... 아직도 구분을 하라고 하면 몇개는 틀릴거같다. 오늘 Spread/Rest를 정리하면서 이터러블 객체를 접하게 돼서 나름 소득이 있는거 같다 빠른 시일 내에 iteratora와 iterable을 공부하고 정리해볼 계획이다

___  
### 참고 사이트
  * https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax
  * https://ko.javascript.info/rest-parameters-spread
  * https://ko.javascript.info/iterable