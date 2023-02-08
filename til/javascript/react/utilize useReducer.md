>bulder.id에 포스팅된 steve sewell: a Cure for React useState Hell? 의 번역본인 velog-eunbinn: useState 지옥에서 벗어나기를 기반으로 useReducer의 활용법들을 정리한다.

## 기본적인 사용 방법
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
공식문서에서 소개하는 가장 기초적인 활용 방안으로 Redux같이 reducer 함수에 dispatch함수를 통해 action을 전달해 state를 조작한다.<br>
간단한 컴포넌트에서는 큰 장점을 발휘하지 못하지만 로직이 복잡해지면 진가를 발휘한다.

하지만 위의 사용법만이 전부는 아니다 redux스타일의 action 패턴외에도 다양한 활용 패턴이 존재하며, 이때문에 useReducer가 useState의 대처방안으로 불리운다.

## 상대 변환 제어(간단한 로직도 매력적)
useReducer가 기본적으로 복잡한 로직에서 쓰인다고 하지만<br>
간단한 컴포넌트에서도 useReducer는 상태 변환을 제어할 수 있고, 각 상태의 변화가 안정적이고 유효할 것을 보장하는 함수를 추가로 인자에 전달해(reducer 함수) 상태 변환 제어를 useState보다 간결하게 처리할 수 있다.

* 기본적인 사용방법에서 사용했던 코드를 단순화 시켜 useState로 만들어 보면 다음과 같이 작성할 수 있다.
	```jsx
	import { useState } from "react";

	function Counter() {
		const [count, setCount] = useState(0);

		return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
	}
	```

* 위의 코드는 증가만 가능하고, 상한선이 존재하지 않는다. 만약 카운트가 10에서 멈춰야 한다면 다음과 같이 작성할 수 있다.
	```jsx
	function Counter() {
		const [count, setCount] = useState(0);
		
		const handleCount = () => {
			if (count < 10) {
				setCount(count + 1);
			}
		}

		return <button onClick={handleCount}>Count: {count}</button>;
	}
	```

* useReduser의 경우에는 더욱 간결해진다.
	```jsx
	function Counter() {
		const [count, setCount] = useReducer((prev, next) => Math.min(next, 10), 0);

		return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
	}
	```

## useState 지옥 벗어나기
```jsx
import { useState } from "react";

function EditCalendarEvent() {
  const [startDate, setStartDate] = useState();
  const [endDate, setEndDate] = useState();
  const [title, setTitle] = useState("");
  const [description, setDescription] = useState("");
  const [location, setLocation] = useState();
  const [attendees, setAttendees] = useState([]);

  return (
    <>
      <input value={title} onChange={(e) => setTitle(e.target.value)} />
      {/* ... */}
    </>
  );
}
```
위와 같이 state가 너무도 많고, 세이프가드가 없는 달력 업데이트 컴포넌트가 있다고 했을 때 상태를 관리하기 위해서 하나의 state 합쳐서 만들 수 있다.

위와 같은 너무도 많고, 종료 날싸를 시작 날짜 이전으로 선책하는 모순이나, 제목이나 설명이 너무 긴 경우에 대한 세이프 가드도 없는 달력 이벤트를 업데이트 하는 컴포넌트가 있다고 했을 때
```jsx
import { useState } from "react";

function EditCalendarEvent() {
  const [event, setEvent] = useState({
    title: "",
    description: "",
    attendees: [],
  });

  return (
    <>
      <input
        value={event.title}
        onChange={(e) => setEvent({ ...event, title: e.target.value })}
      />
      {/* 생략 */}
    </>
  );
}
```
하지만 이렇게 코드를 작성할 경우 이벤트에 항상 `...event`로 전개해 객체를 직접 변경하지 않도록 해야한다.<br> 이를 보완해도 각 이벤트에 맞게 handler도 따로 정의해야 한다. 세이프가드와 안정성까지 고려하면 코드가 점점 커질 것이다.

useReducer의 reducer 함수를 사용하면 이런 문제들이 상당히 깔끔하게 해결된다.
```jsx
import { useReducer } from "react";

function EditCalendarEvent() {
  const [event, updateEvent] = useReducer((prev, next) => {
      const newEvent = { ...prev, ...next };
      // 시작 날짜가 종료 날짜 이후가 될 수 없음
      if (newEvent.startDate > newEvent.endDate) {
        newEvent.endDate = newEvent.startDate;
      }
      // 제목이 100자를 넘을 수 없음
      if (newEvent.title.length > 100) {
        newEvent.title = newEvent.title.substring(0, 100);
      }
      return newEvent;
    },
    { title: "", description: "", attendees: [] }
  );

  return (
    <>
      <input
        value={event.title}
        onChange={(e) => updateEvent({ title: e.target.value })}
      />
      {/* 생략 */}
    </>
  );
}
```
useReducer에 전달된 reducer 함수는 상태를 한곳에서 관리하며 언제나 유효하다는 것을 보장한다는 이점을 가진다.<br>
따라서 이후에 다른 코드들이 추가 되고, 유효하지 않은 데이터가 `updateEvent()`와 함께 호출 되어도 reducer가 상태 값을 검증할 것이다.


## 상태 끌어 올리기 (reducer 공유)
useState를 이용한 기존 상태 끌러올리기는 state가 여러개 존재할때 여러 개의 함수를 전달해야 하지만 useReducer는 useContent와 함께 dispatch 함수 하나만 전달하면 된다.
* 상위 컴포넌트
	```jsx
	const TodosDispatch = React.createContext(null);

	function TodosApp() {
		// 참고: `dispatch` 는 리렌더링 간에 변하지 않는다
		const [todos, updateTodos] = useReducer(todosReducer);

		return (
			<TodosDispatch.Provider value={updateTodos}>
				<DeepTree todos={todos} />
			</TodosDispatch.Provider>
		);
	}
	```
* 하위 컴포넌트
	```jsx
	function DeepChild(props) {
		// action을 수행하고 싶다면 context로부터 dispatch를 전달받으면 된다
		const updateTodos = useContext(TodosDispatch);

		function handleClick() {
			updateTodos({ type: "add", text: "hello" });
		}

		return <button onClick={handleClick}>Add todo</button>;
	}
	```
이렇게 하면 통일된 하나의 업데이트 함수를 가질 수 있을 뿐 아니라 하위 컴포넌트로부터 상태가 업데이트 되어도 요구사항에 부합하도록 안전성을 보장할 수 있다.

## (optional) 불변성 라이브러리[immer]
immer는 가변적인 DX를 가지면서도 데이터의 불변을 보장하는 라이브러리이다.

use-immer 패키지는 `useImmerReducer` 함수를 제공하는데 이 함수를 사용하면 직접적인 변경을 통한 상태 변경이 가능해진다. 라이브러리 내부적으로 자바스크립트 Proxy를 사용해서 불변한 복사본을 만들어 준다.
```jsx
import { useImmerReducer } from "use-immer";

function reducer(draft, action) {
  switch (action.type) {
    case "increment":
      draft.count++;
      break;
    case "decrement":
      draft.count--;
      break;
  }
}

function Counter() {
  const [state, dispatch] = useImmerReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </>
  );
}
```

## useReducer 사용 시기
보통의 경우 `useState`를 사용해도 괜찮다. 상태와 검증 조건들이 복잡해지기 시작하며 추가적인 노력이 들어가기 시작하나독 느껴지면 그때 점진적으로 `useReducer`를 고려해도 좋다.

복잡한 객체들에 `useReducer`를 사용하고, 상태 변경에 따른 위험에 자주 직면할 떄 `Immer`의 사용을 고려해 볼 수 있다.

>steve sewell: don’t forget, start simple, and add complexity only as needed.

## 결론
개인적인 생각으로 위에서 언급한 useReducer의 사용예시는 useState로는 할 수 없는 구현을 useReducer로 할 수 있다는 의미는 아니라고 본다. state를 하나로 만들어서 handler 안에서 상태 변경 제어를 할 수도 있고, useCallback을 통해서 handler의 유효성을 설정할 수 있기때문이다.

핵심은 useReducer의 가능성을 재조명하는 것이라는 생각이 들고, 생각해보지 못했던 활용 방식 덕분에 useReducer를 바라보는 시작이 조금은 변한것 같다. 

좋은 글을 번역해서 공유해주시는 분들에게 참 감사하고 언젠가는 직접 원본을 읽으면서 공부할 수 있었으면 좋겠다.

___
**참고 사이트**
>Builder.io-steve sewell: a Cure for React useState Hell?
* https://www.builder.io/blog/use-reducer#thank-you-david

>velog-eunbunn.log-eunbinn: useState 지옥에서 벗어나기
* https://velog.io/@eunbinn/a-cure-for-react-useState-hell