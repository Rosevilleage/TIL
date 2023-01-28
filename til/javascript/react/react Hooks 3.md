>추가적인 react hooks 2

### useRef
```jsx
const refContainer = useRef(initialValue);
```
`useRef` 는 `.current` 프로퍼티로 전달된 인자(`initialValue`)로 초기화된 변경 가능한 ref 객체를 반환한다.

* 반환된 객체는 컴포넌트의 전 생애주기를 통해 유지된다.
```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```
본질적으로 useRef는 .current 프로퍼티에 변경 가능한 값을 담고 있는 “상자”와 같다. 그리고 useRef는 클래스에서 인스턴스 필드를 사용하는 방법과 유사하게 어떤 가변값을 유지하는 데에 유용하다.

리랜더링은 변수 및 함수의 초기화를 가져온다. 이는 참조값이 변경됨을 의미한다.
<br>그래서 다른 두 함수에서 하나의 변수를 공유해야 할때 useRef가 유용하다.

이는 useRef는 매번 렌더링을 할 때 동일한 ref 객체를 제공하기 때문이다.
useRef는 내용이 변경될 때 그것을 알려주지 않고, current 프로퍼티는 변형되어도 리렌더링을 발생시키지 않는다.

### useImperativeHandle
```jsx
useImperativeHandle(ref, createHandle, [deps])
```
ref를 사용할 때 부모 컴포넌트에 노출되는 인스턴스값을 사용자(customizes)화 한다. `useImperativeHandle`는 `forwardRef`와 같이 사용한다.
```jsx
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```
`<FancyInput/>`를 랜더링한 부모 컴포넌트는 `inputRef.current.focus()`를 호출할 수 있다.

### useLayoutEffect
useEffect와 동일하지만 한가지 차이점은 모든 DOM변경 후에 동기적으로 발생한다는 점이다. 

DOM에서 레이아웃을 읽고 동기적으로 리렌더링하는 경우에 사용하는 것이 좋다. 

useLayoutEffect의 내부 갱신은 브라우저가 화면을 그리기 이전 시점에 동기적으로 수행된다. 클래스 컴포넌트의 `componentDidMount`나 `componentDidUpdate`와 동일한 단계를 실행한다.

___
**참고 사이트**
>React-Hook-Hook API 참고서
* https://ko.reactjs.org/docs/hooks-reference.html