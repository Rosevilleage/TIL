* ## 스코프(범위)
  scope는 직역하자면 **범위**이며, javascript같은 프로그래밍에서는 **변수에 접근할 수 있는 범위**를 말한다.
 
  * 스코프의 종류는 전역 스코프, 지역 스코프로 나뉜다. 
    * **전역 스코프(Global scope):**
       코드 어디에서든지 참조 가능
       전역범위의 변수를 "전역변수"라 칭한다
    
    * **지역 스코프(Local scope, Function-level scope):**
       함수 혹은 코드블록에 의해 생성된 스코프, 함수 자신과 자식 함수에서 혹은 블록 내에서만 참조 가능 
       지역범위의 변수를 "지역변수"라 칭한다.
       
    ```javascript
    let foo = 'global scope';
     
     function example() {
      let bar = 'local scope';
      console.log(bar, foo);
     }

     console.log(foo); // golbal scopr
     console.log(bar); // Uncaught ReferenceError: bar is not defined, 함수 외부에서 참조 불가
     example(); // local scope global scope, 전역변수(전역스코프의 변수) 어디서든지 참조 가능
    ```
    
    * **함수 레벨 스코프(Function-level scope)**
     함수 내에서 선언된 변수는 **함수 내에서만 유효**하며, 함수 외부에서는 참조할 수 없다. <br/> *+ javascript는 기본적으로 함수 레벨 스코프를 따른다.*
   
    * **블록 레벨 스코프(Block-level scope)** 
     모든 코드 블록(함수, `if` 문, `for` 문, `while` 문, `try/catch` 문 등) 내에서 선언된 변수는 **코드 블록 내에서만 유효**하며 코드 블록 외부에서는 참조할 수 없다. 즉, 코드 블록 내부에서 선언한 변수는 지역 변수이다. <br> *+ `let`, `const` 키워드로 선언된 변수는 블록 레벨 스코프를 따른다.*
     
     
  * javascript에서는 함수를 선언한 위치에 따라 상위 스코프가 정해진다.
     함수를 어디서 호출했는지는 영향을 미치지 않는다.
  
  


* ## 호이스팅과 TDZ
  * **호이스팅**이란 `let`, `const`를 포함한 모든 선언(`var`, `let`, `const`, `function`, `class`)를 *스코프의 선두*로 옮긴 것처럼 동작하는 특성을 말한다.<br/> 이는 변수와 함수의 **메모리 공간**을 선언 전에 미리 할당하는 것을 의미한다.
  
  * **TDZ(Temporal Daed Zone; 일시적 사각지대)** 란 스코프의 시작지점부터 초기화 시작지점까지의 구간을 말하며, <span style= "color: orange">**스코프에 변수를 등록**한 후 변수를 위한 **메모리 공간**이 아직 확보되지 않은 구간</span>을 의미한다. <br/> + *메모리의 공간 확보는 초기화 단계에서 이루어 진다.*
  

* ## const, let, var 차이
  변수 선언에 사용되는 `const`, `let`, `var` 중 let과 const는 ES6에 추가된 키워드이다. 그래서 키워드들의 차이는 크게 `var`와 `[let, const]`간에 나타난다.
 
  이 둘의 가장 큰차이점은 변수의 생성 방식에 있다.
  * `let`과 `const`는 변수가 생성될때 **선언-초기화-할당** 세 가지의 단계를 가진다.
    ```javascript
    // 스코프 선두에서 선언 단계가 실행, 호이스팅
     // 번수 선언문 이전에 초기화 되지 않음
     // 변수 선언문 이전에 변수 참조 불가능
    
    console.log(foo); // ReferenceError: foo is not defined
    /* 
    <TDZ(일시적 사각지대, Temporal Dead Zone)>
    */
    let foo; // 선언문에서 초기화 단계가 실행
    console.log(foo); // undefined
    
    foo = 'hello'; // 할당문에서 할당 단계가 실행
    console.log(foo); // hello
    ```
  
  * 그러나 `var`는 선언과 초기화가 **동시**에 진행되고 그 다음에 할당하는 두 단계를 가진다.
    ```javascript
    // 스코프 선두에서 선언과 초기화 단계가 실행, 호이스팅
     // 변수 선언문 이전에 변수 참조 가능
  
     console.log(foo); // undefined
    
     var foo; //
     console.log(foo); // undefined
  
     foo = 'hello'; // 할당문에서 할당 단계가 실행
     console.log(foo); // hello
    ```
 
  * 중복선언, 재할당
    * `var`의 경우는 중복선언과 재할당이 가능하다. 그러나 `let`과 `const`에서는 중복선언을 허용하지 않으며, const는 재할당도 허용하지 않는다(그렇지만 '불변'하지는 않다).
    ```javascript
    // 중복선언
    var foo = 'hi';
    var foo = 'bye'; // 중복선언 허용

    let bar = 'hi';
    let bar = 'bye'; // Uncaught SyntaxError: Identifier 'bar' has already been declared

    // 재할당
    var foo = 'apple';
    foo = 'banana'; // banana, 재할당 허용

    let bar = 'apple';
    bar = 'banana'; // banana, 재할당 허용

    const foobar = 'apple';
    foobar = 'banana'; // Uncaught TypeError: Assignment to constant variable
    ```
  _____
  **+2023_1_1 추가**
  
  * let의 특징
    * let(const 포함)을 사용하면 블록 명령문이나 let을 사용한 표현식 내로 범위가 제한되는 변수를 선언할 수 있다. 이는 let이 var 키워드와 다른 점으로, var는 변수를 블록을 고려하지 않고 현재 함수 (또는 전역 스코프) 어디에서나 접근할 수 있는 변수를 선언한다. 또한 let은 파서가 구문을 평가해야만 변수를 값으로 초기화(아래 참고)한다는 점도 var와 다르다.

    * const와 마찬가지로 let 역시 전역 범위 선언에 사용(최상위 스코프 선언)해도 window 객체에 새로운 속성을 추가하지 않는다.(var의 경우에는 추가한다.)
    
    1. 스코프 규칙
    let으로 선언한 변수는 자신을 선언한 블록과 모든 하위 블록을 스스로의 스코프로 가진다. 이런 점에서는 let이 var와 유사하지만 둘의 중요한 차이는, var의 경우 스코프가 '자신을 선언한 블록'이 아니라, 자신의 선언을 포함하는 함수라는 점이다.
    ```javascript
    function varTest() {
      var x = 1;
      if (true) {
        var x = 2; // 같은 변수
        console.log(x); // 2
      }
      console.log(x); // 2
    }

    function letTest() {
      let x = 1; // 여기를 var로 선언해도 결국 다른 변수
      if (true) {
        let x = 2; // 다른 변수
        console.log(x); // 2
      }
      console.log(x); // 1
    }
    ```
    2. 어휘적 스코프와 결합한 TDZ
    ```javascript
    function test(){
      var foo = 33;
      if(foo) {
        let foo = (foo + 55); // ReferenceError
       }
    }
    test();
    ```
    바깥 스코프의 `var foo`가 값을 가지므로 if 블록은 true로 평가된다. 그러나 어휘적 스코프로 인해, `var foo`의 값은 블록 내에서 사용할 수 없는데 내부의 foo 식별자는 `let foo`를 가리키기 때문이다. 따라서 `(foo + 55)` 표현식은 `let foo`의 초기화가 끝나지 않은, 즉 TDZ의 내부이며 ReferenceError가 발생하게 된다.
    3. 스코프의 변환
    블록 내에서 사용한 경우 let은 변수의 스코프를 해당 블록으로 제한한다. var는 스코프를 함수로 제한한다.
    ```javascript
    var a = 1;
    var b = 2;

    if (a === 1) {
      var a = 11; // 전역 변수
      let b = 22; // if 블록 변수
      console.log(a);  // 11
      console.log(b);  // 22
    }

    console.log(a); // 11
    console.log(b); // 2
    ```
    var와 let을 아래와 같이 사용하면 SyntaxError가 나온다. 호이스팅으로 인해 var가 블록 최상단으로 끌어올려져, 변수 재선언을 하는 것과 같아지기 때문이다.
    ```javascript
    let x = 1;
    {
      var x = 2; // 재선언으로 인한 SyntaxError
    }
    ```
  ____
 * ## var vs let vs const
   1. 중복선언은 프로젝트의 크기가 커질 수 록 예상치 못한 에러가 발생할 가능성이 높아 지양하는 것이 좋다.
   
   2. 또한 의도치 않은 재할당을 피하는 것이 좋다.
   
   * 변수선언에는 기본적으로 `const`를 사용하고 재할당이 필요할 경우 let을 한정적으로 사용하는 것이 좋다.
     * ES6를 사용한다면 `var`키워드는 사용하지 않는다.
     * `let` 키워드를 사용하는 경우 변수의 스코프는 최대한 좁게 만든다.
     * 변경이 발생하지 않는 원시값과 객체에는 `const`를 사용한다.
     * 재할당의 여부를 선언시점에 바로 알기 어려우니 일단 `const`를 사용하고 필요할 때 바꾸는 것도 나쁘지 않은 방법이다.
    