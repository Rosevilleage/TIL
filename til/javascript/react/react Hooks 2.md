>추가적인 react hooks

## useReducer
useState의 대체 함수로 `(state, action)=>newState` 의 형태로 reducer를 받고 dispatch 메서드와 짝의 형태로 현재 state를 반환한다.
```jsx
const [state, dispatch] = useReducer(reducer, initialArg, init);
```
다수의 하윗값을 포함하는 복잡한 정적 로직을 만드는 경우나 다음 state가 이전 state에 의존적인 경우에 보통 useState보다 useReducer를 선호한다.

또한 useReducer는 콜백 대신 dispatch를 전달할 수 있기 때문에 자세한 업데이트를 트리거하는 컴포넌트의 성능을 최적화 할 수 있다.
```jsx
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### 초기화, 초기화 지연
* state의 초기화가 필요한 경우 useReducer에 초기값을 두번째 인자로 전달해 사용할 수 있다.
	```jsx
	const [state, dispatch] = useReducer(reducer, initialArg)
	```
* 초기 state를 조금 지연해서 생성하고자 하는 경우 `init` 함수를 reducer의 세 번째 인자로 전달하면된다. 그러면 초기 state는 `init(initialArg)` 에 설정된다.
```jsx
function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    {/*..생략*/}
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      {/*..생략*/}
    </>
  );
}

```
### dispat의 회피
Reducer Hook에서 현재 state와 같은 값을 반환하는 경우 React는 `Object.is 비교 알고리즘` 을 사용해 자식의 리렌더링이나 effect를 발생시키지 않는다.
___
>## memoization
메모이제이션은 직역하면 '메모리에 넣기'라는 의미이며,

컴퓨터프로그램이 동일한 계산을 반복해야 할 때 이전에 계산한 값을 메모리에 저장함으로써 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 동적 계획법의 핵심이 되는 기술이다.<br>(memorization[메모라이제이션]과는 다른 개념이다.)

javascript에서는 클로저, 고차함수, 재귀함수 등을 통해 메모이제이션 컨셉을 구현할 수 있다.
___
## useCallback
메모이제이션된 콜백을 반환한다.

`useCallback(fn, deps)` 은 `useMemo(()=>fn, deps)` 와 같다.
```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],);
```
첫 번째 인자에 콜백 함수를 받고, 두 번째 인자에 의존성 값을 배열 형태로 받는다.

메모이제이션된 콜백은 의존성 값이 변경되었을 때만 변경된다.

불필요한 렌더링을 방지하기 위해 (예로 shouldComponentUpdate를 사용하여) 참조의 동일성에 의존적인 최적화된 자식 컴포넌트에 콜백을 전달할 때 유용하다.(추후에 SCU공부하기)

## useMemo
메모이제이션된 값을 반환한다.
```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```
첫 번째 인자에 함수를 받아 메모이제이션해 반환활 생성함수가 필요하고, 두 번째 인자에 의존성 값을 배열 형태로 받는다.

`useMemo`는 의존성이 변경되었을 때에만 메모이제이션된 값만 다시 계산한다. 이 최적화는 모든 렌더링시의 고비용 계산을 방지하게해준다.

`useMemo`로 전달된 함수는 렌더링 중에 실행된다. 통상적으로 렌더링 중에는 하지 않는 동작을 넣어선 안된다. 사이드 이펙트(side effects)는 `useEffect`로 처리해야 한다.

### 최적화
react는 state가 변경될 때, props가 변경될 때, 부모 컴포넌트가 리렌더링 되는 경우 그리고 Context value나 redux store 등 중앙 상태값이 변경될 때 리렌더링이 일어난다.

리렌더링이 일어날 때마다 함수는 재생성된다. 만약 props로 함수를 전달한다면, props를 받는 컴포넌트에서 `useMemo, React.memo` 등으로 최적화를 했다고 하더라도 하위 컴포넌트의 리렌더링이 발생한다.

이러한 리렌더링에서 불필요한 연산이나, 다른 컴포넌트의 리렌더링으로 성능의 저하가 일어나는 것을 막기 위해 `useMemo`와 `useCallback`이 사용된다.

### useMemo, useCallback 차이

`useMemo` 는 메모이제이션된 `값` 을 반환한다.
* 그래서 메모리를 더 사용하더라도 불필요한(중복) 연산을 막을 필요가 있는 비싼(expensive) 계산을 하는 함수에 사용한다.
* 또한 자식 컴포넌트에서 특정 props 값의 변화를 최적화 하고 싶을때 사용한다.

`useCallback` 은 메모이제이션된 `함수` 를 반환한다.
* 자식 컴포넌트에 props로 함수를 전달하는 경우

	함수는 참조자료형으로 생성될 때마다 다른 참조를 가진다. 리렌더링을 함수를 재생성한다. 즉 리렌더링 시마다 함수가 변경된다는 것이다. 이 함수가 props로 전해진다는 것은 필히 하위 컴포넌트의 리렌더링을 동반한다.

	때문에 props로 함수를 넘겨줄 때는 useCallbak을 통해 메모이제이션 된 함수를 넘겨서 의존성의 변경 외에는 리렌더링이 발생하지 않게 한다.
* 외부값을 가져오는 API를 호출할 경우
	
	함수 안에서 api를 호출하면 리렌더링 시마다 함수는 재생정되고 결국 api는 리렌더링 시 마다 호출된다. 이러한 부분을 막기 위해서 `useCallback`을 통해 함수가 변경되지 않게 한다.


___
**참고 사이트**
>React-Hook API 참고서
* https://ko.reactjs.org/docs/hooks-reference.html#specifying-the-initial-state

>위키백과-메모이제이션
* https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EC%9D%B4%EC%A0%9C%EC%9D%B4%EC%85%98

>Tistory-시간이 멈추는 장소-기은P-[React] useMemo와 useCallback의 차이
* https://narup.tistory.com/273