> javascript에서는 자료(데이터)를 메모리에 보관하는 방법에 따라 자료형을 두 가지로 구분짓는다.

## 원시 자료형(primitive data type)
객체가 아니면서 메서드를 갖지 않는 7가지의 데이터 타입: `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, `null`
* 모든 원시 값은 불변하여 변형할 수 없고, 변수는 새로운 값을 다시 할당할 수 있다. 이미 생성한 원시 값은 객체, 배열, 함수와는 달리 **변형할 수 없다.**
* 원시 값은 콜 스택(call stack)상에서 식별자 주소를 통해 값을 가져오고, 변수 식별자 자체는 콜스택 상의 실행컨텍스트(Execution Context)의 렉시컬 환경(Lexical Environment)에 저장된다
 ```javascript
 // 문자열 메서드는 문자열을 변형하지 않음
 var bar = "baz";
 console.log(bar);        // baz
 bar.toUpperCase();
 console.log(bar);        // baz

 // 배열 메소드는 배열을 변형
 var foo = [];
 console.log(foo);        // []
 foo.push("plugh");
 console.log(foo);        // ["plugh"]

 // 할당은 원시 값에 새로운 값을 부여 (변형X, 'BAZ'라는 값을 가지는 메모리 주소로 교체)
 bar = bar.toUpperCase(); // BAZ
 ```
 원시 자료형은 식별자 주소를 교체할 수는 있지만, 값 자체를 변형할 수는 없다.
 
 * 우리가 원시 자료형에 메서드를 사용할 수 있는 것은 `null`, `undefined`를 제외한 원시 값들이 **원시 값을 래핑한 객체를 가지기때문이다.** string을 예로 들자면 원시 값(string)자체의 메서드가 아닌 string을 래핑한 **객체의 메서드**를 사용하는 것이다.
 
## 참조 자료형(reference data type)
 원시 자료형을 제외한 모든 자료형 ex) 배열, 객체, 함수
 *  참조 자료형의 데이터는 동적으로 크기가 변하는 메모리 힙(memory heap)에 저장되고, 콜 스택에 할당 될 주소를 가진다. 
 ![](https://velog.velcdn.com/images/cksgml1914/post/8a62ca2e-381f-4716-a494-d799700c7e6a/image.png)


 * 참조 자료형은 원시 자료형과 달리 주소를 통해 데이터에 접근하고 동적이기 때문에 값의 변형이 가능하다.
 
   * 원시 자료형은 변수 간에 데이터를 복사할 경우 데이터 값을 복사해 원본 데이터를 교체하지 않는다.
    ```javascript
    let a=1;
    b=a;
    b=2
    console.log(a); // 1
    ```
   * 참조 자료형은 변수 간에 데이터를 복사할 경우 주소를 복사해 원본데이터를 변형시킬 수 있다.
    ```javascript
    let a=[1, 2, 3];
    let b=a;
    b[0]=5;
    console.log(a); // [5, 2, 3]
    ```
    값의 변형을 원하지 않는다면 깊은 복사(deep copy)를 통해 두 변수의 참조 주소를 다르게 하는 것이 좋다.
    ```javascript
    const arr = [1, 2, 3];
    const newArr = [...arr]; // 다른 메모리 주소를 가짐
    newArr[0] = 5;
    console.log(arr, newArr); // [1, 2, 3] [5, 2, 3]
    ```