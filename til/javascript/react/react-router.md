>react의 spa 도구중 하나인 router 라이브러리에 대해 알아본다.

## SPA(Single Page Application)
단 한개의 페이지로 이루어진 애플리케이션.

기존의 웹 페이지는 페이지가 변경될 때마다 화면 전체를 렌더링해 새로고침 현상을 보이지만 SPA는 변경이 되는 컴포넌트만 재 렌더링해 새로고침이 없이 부드러운 화면 전환으로 사용자 경험을 좋게 만들어 준다.

또한 필요한 부분만 업데이트 하기때문에 서버 자원과 트래픽의 부담을 줄여준다.

## Router
react에서 SPA(Single Page Application)을 구현할 때 사용하는 서드파티 라이브러리로 router외에 다른 선택지도 존재한다. ex) Next.js

### 라우팅(routing)
사용자가 요청한 URL에 해당하는 페이지를 브라우저를 통해 보여주는 것

### BrowserRouter, Routes, Route
```javascript
import { BrowserRouter, Route, Routes } from 'react-router-dom';
import About from './pages/About';
import Home from './pages/Home';
import Products from './pages/Products';

const App = () => {
  return (
		<BrowserRouter> {/*실제로 사용할 때는 render()가 있는 최상위 컴포넌트에서 사용*/}
			<Routes>
				<Route path="/" element={<Home />} />
				<Route path="/about" element={<About />} />
				<Route path="/products/:productname" element={<Products />} />
			</Routes>
		</BrowserRouter>	
  );
};

export default App;
```

* BrowserRouter

	라우터의 최상위 컴포넌트, 웹 애플리케이션에 HTML5의 History API를 사용하여 페이지를 새로 불러오지 않으면서 주소를 변경하고, 현재 주소의 경로에 관련된 정보를 리엑트 컴포넌트에서 사용할 수 있게 해준다.

* Routes

	여러 Route를 감싸고, 주소가 변경될 때마다  하위 Route 중 주소가 일치하는 Route 하나만을 렌더링 시켜준다.

* Route

	URL세그먼트를 구성 요소, 데이터 로드 및 데이터 변형에 연결한다.
	
	경로 중첩을 통해 복잡한 애플리케이션 레이아웃과 데이터 종속성이 단순하진다. 코드를 선언적으로 구성한다.
	1. path : 경로가 URL, 링크 또는 양식 작업과 일치하는지 확인하기 위해 URL과 일치시킬 경로 패턴

		여러가지 라우팅을 매칭하고 싶은 경우 URL 뒤에 *을 추가해 사용한다.

		{*} 하나만 사용할 경우 사용자가 사전에 정의 하지 않은 경로로 접근했을 때 *을 path로 설정한 Route를 렌더링 한다. 

	2. Element : 경로가 URL과 일치할 때 렌더링할 요소
### Link, NavLink
* Link

	사용자가 클릭하거나 탭하여 다른 페이지로 이동할 수 있게 해주는 요소.
	
	to 속성에 이동할 경로를 설정해 사용한다. <br>
	History API를 통해 브라우저 주소의 경로만 변경한다.

* NavLink

	Link의 special version으로 스타일을 지정할 수 있다.

	현재 페이지의 주소와 to의 경로가 같으면 activeStyle, activeClassName에 지정된 style과 className이 활성화 된다. 

### URL 파라미터, 쿼리스트링
페이지 주소를 유동적인 값으로 정의하는 방법
* URL 파라미터(URL parameter) ex) /products/:productId

	주소의 경로에 유동적인 값을 설정 <br>
	여러개를 사용할 경우 연결해 사용 ex) /:id/:name <br>
	id나 이름 등을 사용하여 특정 데이터를 조회할 때 사용
	* 연관 hook
	
		useParams() : Route의 path를 통해 URL파라미터를 객체 형태로 조회
	
* 쿼리스트링(Querystring) ex) /products?page=1&productName=example

	주소의 뒷부분 `?` 문자열 이후에 key=value 쌍으로 정의하고, `&` 로 구분

	키워드 검색, 페이지네이션, 정렬 방식 등 데이터 조회에 필요한 옵션을 전달할 때 사용
	* 연관 hook

	1. useLocation() : 현재 페이지의 정보를 객체 형태로 반환
		* pathname : 쿼리스트링 제외을 제외한 현재 주소의 경로
		* search : ? 문자를 포함한 쿼리스트링 값
		* hash : #문자열 뒤의 값(주로 History API가 지원되지 않는 구형 브라우저에서 클라이언트 라우팅을 사용할 때 쓰는 해시 라우터에서 사용)
		* state : 페이지로 이동할 때 임의로 넣을 수 있는 상태 값
		* key : location 객체의 고유 값, 초기값은 default, 페이지가 변경될 때마다 고유값 생성
	2. useSearchParams() : 배열 타입의 값을 반환
		```javascript
		import { useSearchParams } from 'react-router-dom';
		const [searchParams, setSearchParams] = useSearchParams();
		
		const detail = searchParams.get('detail');

		const onToggleDetail = () => {
			setSearchParams({ mode, detail: detail === 'true' ? false : true });
		};
		```
		* 첫번째 원소의 get메서드로 특정 쿼리파라미터를 조회, set메서드를 통해 업데이트 가능. 쿼리파라미터가 존재하지 않으면 null로 조회
		* 두번째 원소는 쿼리파라미터를 객체 형태로 업데이트할 수 있는 함수를 반환

___
**참고 사이트**
>ReactRouter-React Router: Home v6.7.0
* https://reactrouter.com/en/main/route/route

>velog-velopert.log : velopert-React Router v6 튜토리얼
* https://velog.io/@velopert/react-router-v6-tutorial