>기초적인 react hooks

## Hook
hook은 react의 특별한 함수로, class 컴포넌트에서 사용하던 state, 생명주기 기능(lifecycle features) 등의 기능을 함수 컴포넌트에서도 사용할 수 있게 연동(hook into)해 준다.

### Hook의 규칙
1. 컴포넌트 최상위(at the top level)에서만 호출해야 한다.

	그렇지 않으면 react가 hook을 찾을 때 오류가 날 가능성이 다분하다.
2. 오직 react 함수 내에서 호출해야 한다.
	
	react 함수 컴포넌트에서 hook을 호출해야 컴포넌트의 모든 상태 관련 로직을 소스코드 내에서 명확하게 보여줄 수 있다.

* hook을 인식하는 방식
	
	한 컴포넌트 안에 여러개의 hook이 사용됐을 때, 특정 `state`가 어떤 `useState()` 호출에 해당하는지를 알아내기 위해서 react는 hook을 호출 순서에 따라 읽는다. 즉, react은 hook을 동기적으로 읽는다. 이렇게 하면 모든 렌더링에서 hook의 호출 순서가 같기 때문이다. 

	hook이 컴포넌트 최상위 레벨에서 호출되어야 하는 이유가 이 때문이다. 또한 조건부로 effect를 실행하기 원할 경우 조건문을 hook안에 넣으면 된다.

### useState()
```javascript
const [state, setState] = useState(initialState)
```
상태 유지 값과 그 값을 갱신하는 함수를 반환한다.

최초 렌더링을 하는 동안, 반환된 state는 useState()에 전달된 인자의 값과 같다.

setState함수는 state를 갱신할 때 사용하며, 새 state 값을 받아 컴포넌트 리렌더링을 큐에 등록한다.

다음 리렌더링 시에 useState를 통해 반환받은 첫 번째 값은 항상 갱신된 최신 state가 된다.(setState는 함수 동일성이 안정적이고, 리렌더링에도 변경되지 않을 것이 보장된다.)

* 클래스 컴포넌트의 setState 메서드와는 다르게, useState는 갱신 객체(update objects)를 자동으로 합치지 않는다. 함수 업데이터 폼을 객체 전개 연산자와 결합함으로써 흉내낼 수 있다.
	```javascript
	const [state, setState] = useState({});
	setState(prevState => {
		// Object.assign would also work
		return {...prevState, ...updatedValues};
	});
	```
* initialState

	initialState 인자는 초기 렌더링 시에 사용하는 state로 그 이후의 렌더링 시에는 무시된다. 초기 state가 고비용 계산의 결과라면, 초기 렌더링 시에만 실행될 함수를 대신 제공할 수 있다.
	```javascript
	const [state, setState] = useState(() => {
		const initialState = someExpensiveComputation(props);
		return initialState;
	});
	```

### useEffect()
```javascript
useEffect(didUpdate);
```
명령형 또는 어떤 effect를 발생하는 함수를 인자로 받는다.

useEffect에 전달된 함수는 화면에 렌더링이 완료된 후에 수행된다. 기본적으로 모든 동작은 렌더링이 완료된 후에 실행되지만, 두번째 인자에 배열형태로 요소를 넣으면 특정값이 변경되었을 때만 실행되게 할 수도 있다.
* clean-up
	```javascript
	useEffect(() => {
		const subscription = props.source.subscribe();
		return () => {
			// Clean up the subscription
			subscription.unsubscribe();
		};
	});
	```
	effect는 종종 컴포넌트가 화면에서 제거될 때 정리해야 하는 리소스를 만든다. 이를 수행하기 위해서 useEffect로 전달된 함수는 정리(clean-up) 함수를 반환할 수 있다. 

	정리 함수는 메모리 누수 방지를 위해 UI에서 컴포넌트를 제거하기 전에 수행된다. 그리고 컴포넌트가(그냥 일반적으로 수행하는 것처럼) 여러 번 렌더링 된다면 다음 effect가 수행되기 전에 이전 effect는 정리된다.
* 조건부 effect
	```javascript
	useEffect(
		() => {
			const subscription = props.source.subscribe();
			return () => {
				subscription.unsubscribe();
			};
		},
		[props.source],
	);
	```
	effect의 기본적으로 모든 렌더링을 완료한 후 동작한다. 이는 종속성의 변경이나, 렌더링 시 effect가 항상 재생성됨을 의미한다.

	source props가 변경될 때에만 effect가 필요하다면, useEffect에 두 번째 인자에 effect가 종속될 값의 배열을 전달할 수 있다.

	빈 배열 `[]` 을 전달하면, 첫 렌더링 시에만 effect를 생성 한다.
### useContext()
```javascript
const value = useContext(MyContext);
```
context 객체(React.createContext에서 반환된 값)를 받아 그 context의 현재 값을 반환한다.

context의 현재 값은 트리 안에서 이 Hook을 호출하는 컴포넌트에 가장 가까이에 있는 `<MyContext.Provider>` 의 `value prop`에 의해 결정된다.

`useContext`로 전달하는 인자는 **context 객체** 그 자체여야 한다.

useContext를 호출한 컴포넌트는 context 값이 변경되면 항상 리렌더링 된다. 컴포넌트를 리렌더링 하는 비용이 많이 든다면, 메모이제이션(useMemo)을 사용하여 최적화할 수 있다.

```javascript
const themes = {
  day: {
    foreground: "black",
    background: "white"
  },
  night: {
    foreground: "white",
    background: "black"
  }
};

const ThemeContext = React.createContext(themes.day);

function App() {
  return (
    <ThemeContext.Provider value={themes.night}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      change theme
    </button>
  );
}
```

___
**참고 사이트**
>React-문서-HOOK
* https://ko.reactjs.org/docs/hooks-intro.html