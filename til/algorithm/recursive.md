> 재귀함수에 관해 내가 어려워했던 문제들과 함께 정리한다.

## 재귀
컴퓨터 과학에서 재귀는 자신을 정의할 때 자기 자신을 재참조하는 방법을 뜻하며, 이를 프로그래밍에 적용한 재귀 호출의 형태로 많이 사용된다.

재귀는 큰 목표 작업 하나를 동일하면서 간단한 작업 여러 개로 나눌 수 있을 때 유용한 프로그래밍 패턴이며, 목표 작업을 간단한 동작 하나와 목표 작업을 변형한 작업으로 단순화시킬 수 있을 때도 재귀를 사용할 수 있다. 그리고 특정 자료구조를 다뤄야 할 때도 재귀가 사용된다.

### 재귀 깊이 (recursion depth)
가장 처음 하는 호출을 포함한 중첩 호출의 최대 개수를 의미한다.

자바스크립트 엔진은 최대 재귀 깊이를 제한하는데 만개 정도까진 거의 대부분 허용하고, 엔진에 따라 이보다 더 많은 깊이를 허용하는 경우도 있다.(대다수의 엔진이 십만까지는 다루지 못한다) 이런 제한을 완화하려고 엔진 내부에서 자동으로 'tail calls optimization’라는 최적화를 수행하긴 하지만, 모든 곳에 적용되는 것은 아니고 간단한 경우에만 적용된다.

재귀 깊이 제한 때문에 재귀를 실제 적용하는데 제약이 있긴 하지만, 재귀는 여전히 광범위하게 사용되고 있는데 재귀를 사용하면, 간결하고 유지보수가 쉬운 코드를 만들 수 있기 때문이다.

### 실행 컨텍스트와 스택
실행 중인 함수의 실행 절차에 대한 정보는 해당 함수의 실행 컨텍스트(execution context) 에 저장된다.

실행 컨텍스트는 함수 실행에 대한 세부 정보를 담고 있는 내부 데이터 구조다. 제어 흐름의 현재 위치, 변수의 현재 값, this의 값 등 상세 내부 정보가 실행 컨텍스트에 저장되게 된다.

함수 호출 일 회당 정확히 하나의 실행 컨텍스트가 생성되며, 내부에 중첩 호출이 있을 때는 다음과 같다.

* 현재 함수의 실행이 일시 중지
* 중지된 함수와 연관된 실행 컨텍스트는 실행 컨텍스트 스택(execution context stack) 이라는 특별한 자료 구조에 저장
* 중첩 호출이 실행
* 중첩 호출 실행이 끝난 이후 실행 컨텍스트 스택에서 일시 중단한 함수의 실행 컨텍스트를 꺼내오고, 중단한 함수 실행

반복을 사용해 만든 함수는 컨텍스트를 하나만 사용하며, 이 컨텍스트에서 변수가 변경된다. 실행 컨텍스트가 하나이기 때문에 필요한 메모리가 적고, 사용 메모리 공간도 고정된다.

재귀를 이용해 작성한 코드는 반복문을 사용한 코드로 다시 작성할 수 있다. 반복문을 사용하면 대개 함수 호출의 비용(메모리 사용)이 절약된다.
하지만 모든 곳에서 메모리 최적화를 신경쓰면서 코드를 작성해여 하는 것은 아니기 때문에 재귀 함수의 사용을 무조건 지양하지는 않는 것이다.

## 재귀 함수
재귀 함수는 자기 자신을 호출하는 함수를 의미하며, 반복문과 비슷한 용도로 사용할 수도 있다.

내가 이해한 재귀함수의 기본적인 구성은 다음과 같다.
1. 함수에 인자로 받아온 데이터를 쪼개가는 식으로 전개하고 최소 범위에 반환할 값을 정해준다.
2. return문 안에 함수 자신을 포함한 필요한 식 혹은 값을 반환시킨다.
3. 점점 작아지는 식에서 호출된 함수는 최소 범위의 값이 정해질 때 까지 대기하다가 최소 값부터 차래대로 식에 값을 대입해가며 연산한다.

이 순서를 만족시키는 기본적인 코드는 다음과 같이 작성할 수 있다.
```javascript
function recursive(input1, input2, ...) {
  // base case
  if (문제를 더 이상 쪼갤 수 없을 경우) {
    return 단순한 문제의 해답;
  }

  // recursive case
  return 더 작은 문제로 새롭게 정의된 문제
}
```

## 여려웠던 or 시간이 좀 걸렸던 문제
### findMatryoshka
* 문제

	러시아 전통인형 마트료시카에 대한 정보를 담은 객체와 수를 입력받아 조건에 맞는 인형이 있는지 여부를 boolean 타입으로 리턴해야 합니다.
* 입출력 예시
	```javascript
	const matryoshka = {
		size: 10,
		matryoshka: {
			size: 9,
			matryoshka: null,
		},
	};

	let output = findMatryoshka(matryoshka, 10);
	console.log(output); // --> true

	output = findMatryoshka(matryoshka, 8);
	console.log(output); // --> false
	```
* 문제 풀이

	처음에는 `matryoshka`에 빈 객체가 들어왔을 경우와 null일 경우로 나눠서 예외 케이스를 정해서 문제를 풀었는데, <br>
	reference에서는 호출과 통과만 조건식으로 정해놓은걸 상황에 따라 조건식을 설정하는게 효율적이라는 생각을 다시 하게 됐다.
	```javascript
	function findMatryoshka(matryoshka, size) {
		if(matryoshka.size===size) return true;
		if(matryoshka.matryoshka&&matryoshka.size>size) return findMatryoshka(matryoshka.matryoshka, size);
		return false;
	}
	```

### unpackGiftbox
* 문제

	선물 상자에 대한 정보를 담은 배열과 문자열을 입력받아 조건에 맞는 선물이 있는지 여부를 boolean 타입으로 리턴해야 합니다.
* 입출력 예시
	```javascript
	onst giftBox = ['macbook', 'mugcup', ['eyephone', 'postcard'], 'money'];

	let output = unpackGiftbox(giftBox, 'iphone');
	console.log(output); // --> false

	output = unpackGiftbox(giftBox, 'postcard');
	console.log(output); // --> true
	```
* 문제 풀이

	중복 배열을 재귀 함수로 다루는게 중점인 문제로, 함수 호출을 무조건적으로 최상위에서 할 필요는 없다는 것 크게 느끼게 된 문제다.<br>
	앞선 문제들을 풀면서 재귀함수를 무조건 어디에 써야한다는 생각을 무의식적으로 가졌었는데 반복문이나 더 나아가 고차함수를 같이 쓸수 있다는 것을 알게 되었다.
	```javascript
	function unpackGiftbox(giftBox, wish) {
		return giftBox.reduce((a,b)=>{
			if(b===wish) return true;
			if(Array.isArray(b)) return unpackGiftbox(b, wish)||a;
			return a;
		}, false)
	}
	<!-- 혹은 다음과 같이 반복문을 사용해 작성할 수 있다. 만약 wish가 2차배열 전에 존재한다면, 성능은 아래 코드가 더 좋다.
	위의 reduce는 무조건적으로 바지막 item까지 확인한다 -->
	function unpackGiftbox(giftBox, wish) {
  for (let i = 0; i < giftBox.length; i++) {
    if (giftBox[i] === wish) {
      return true;
    } else if (Array.isArray(giftBox[i])) {
      const result = unpackGiftbox(giftBox[i], wish);
      if (result === true) {
        return true;
      }
    }
  }
  return false;
	}
	```

### flattenArr
* 문제

	다차원 배열을 입력받아 1차원 배열로 변환하여 리턴해야 합니다.
* 입출력 예시
	```javascript
	let output = flattenArr([[1], 2, [3, 4], 5]);
	console.log(output); // --> [1, 2, 3, 4, 5]

	output = flattenArr([[2, [[3]]], 4, [[[5]]]]);
	console.log(output); // --> [2, 3, 4, 5]
	```
* 문제 풀이

	findMatryoshka와 비슷하게 중복 배열에 관한 문제이지만 반환해야 하는 값이 변황된 배열인 문제이다.<br>
	spread syntax만 생각나고 concat이 생각이 안나서 조금 방황했던 문제였다.
```javascript
function flattenArr(arr) {
  return arr.reduce((a, b)=> {
    if (Array.isArray(b)) {
      return a.concat(flattenArr(b));
    }
    return a.concat(b);
  }, []);
}
다음과 같이 작성할 수도 있다. 아래의 코드가 상대적으로 이해하기 쉽다.
function flattenArr(arr) {
  if (arr.length === 0) return [];
  const [head, ...tail] = arr;
  if (Array.isArray(head)) {
    return flattenArr([...head, ...tail]);
  } else {
    return [head].concat(flattenArr(tail));
  }
}
```

꼬리재귀는 어느정도 이해가 됐지만 간단한 상황 외에는 활용을 어떻게하는지 몰라서 추후에 정리해 볼 계획이다.
___
**참고 사이트**
>위키백과-재귀(컴퓨터 과학)
* https://ko.wikipedia.org/wiki/재귀_(컴퓨터_과학)