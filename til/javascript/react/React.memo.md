>React.memo에 대해서 정리한다.

## React.memo
`React.memo`는 고차 컴포넌트로 컴포넌트가 동일한 props로 동일한 결과를 렌더링한다면, `React.memo`를 호출하고 결과를 메모이징하도록 래핑해 경우에 따라 성능을 향상시킬 수 있다. 이는 컴포넌트를 렌더링하지 않고 마지막으로 렌더링된 결과를 재사용함을 의미한다.
```jsx
const MyComponent = React.memo(function MyComponent(props) {
  /* props를 사용하여 렌더링 */
});
/// 혹은
export default React.memo(MyComponent)
```
React.memo는 props 변화에만 영향을 준다. 이는 memo로 감싼 컴포넌트에서 `useState`, `useReducer`, `useContext`를 사용한다면, 여전히 state나 context가 변할 때 다시 렌더링이 된다는 것을 의미한다.

또한 `React.memo`는 props가 갖는 복잡한 객체에 대해 얕은 비교만을 수행한다. 다른 비교 동작을 원한다면, 두 번째 인자로 별도의 비교 함수를 제공해야 한다.
```jsx
function MyComponent(props) {
  /* props를 사용하여 렌더링 */
}
function areEqual(prevProps, nextProps) {
  /*
  nextProps가 prevProps와 동일한 값을 가지면 true를 반환하고, 그렇지 않다면 false를 반환
  */
}
export default React.memo(MyComponent, areEqual);
```
class 컴포넌트의 `shouldComponentUpdate()` 메서드와 달리, areEqual 함수는 props들이 서로 같으면 `true`를 반환하고, props들이 서로 다르면 `false`를 반환한다.(정반대)

실제 memo의 작동을 살펴보면
```jsx
import React, {useState} from 'react'
import Child from './Child'

function Parent() {
	const [parentCount, setParentCount] = useState(0);
	const [childCount, setChildCount] = useState(0);

	const incrementParentCount = () => {
		setParentCount(parentCount+1)
	}
	const incrementChildCount = () => {
		setChildCount(childCount+1)
	}

	return (
		<div>
			<button onClick={incrementParentCount}>ParentCountUp</button>
			<h2>parent</h2>
			<p>parentCount: {parentCount}</p>
			<button onClick={incrementChildCount}>ChildCountUp</button>
			<Child childCount={childCount}>
		</div>
	)
}
```

위와 같은 컴포넌트가 있을 때 ParentCountUp 버튼을 누르면 parentCount만 증가한다.<br>
 하지만 react에서 state의 변화는 해당 컴포넌트의 리렌더링을 동반하며, 상위 컴포넌트의 리렌더링은 하위 컴포넌트의 리렌더링을 동반한다.

즉, Child 컴포넌트는 입력값과 결과값이 변하지 않았음에도 리렌더링을 해야 한다는 것이다.<br>

```jsx
import React, {memo} from 'react';

const Child = ({childCount}) => {
	return (
		<div>
			<h3>child</h3>
			<p>childCount: {childCount}</p>
		</div>
	)
};

export default memo(Child)
```

Child 컴포넌트를 `React.memo`로 감싼다면, 위의 리렌더링을 해소할 수 있다. <br>
이제 Child는 Parent 컴포넌트가 렌더링 될 때마다 자신의 props가 비교해 달라졌는지 확인하고, 달리지지 않았다면 memoization한 결과를 반환할 것이다.

React공식문서에서는 React.memo를 오직 성능 최적화 만을 위해 사용할 것을 권장한다. 렌더링 방지를 위해 사용하면 버그를 만들 수 있다는 당부와 함께 말이다.

우선적으로 memo는 렌더링이 일어날 때마다. props를 비교한다. 만약 `state`의 변동이나 `sideEffect`가 잦다면 불필요한 비교로 인해 오히려 성능에 안좋은 영향을 끼칠 수 있다.<br>
그렇기 때문에 memo는 컴포넌트가 고정된 값을 반환하는지, 무거운 연산을 수행해 잦은 렌더링에서 성능의 저하를 일으킬 가능성이 있는지 고려한 후에 적용여부를 결정하는 것이 좋다.

___
**참고 사이트**
>React-문서-API 참고서
https://ko.reactjs.org/docs/react-api.html#reactmemo