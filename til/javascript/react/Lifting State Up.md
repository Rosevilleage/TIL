>상태 끌어올리기에 대해 정리한다.

react에서 컴포넌트는 단방향 데이터흐름에 따른다. 그래서 하위 컴포넌트는 자신이 상위 컴포넌트로 부터 받은 데이터의 출처가 state인지 수동입력값인지 알지 못하며, 모든 컴포넌트는 state가 소유하고 설정한 컴포넌트 외에는 특정 컴포넌트가 유상태인지 무상태인지조차 알지 못한다

만약 한 컴포넌트에서의 데이터 변화가 다른 컴포넌트에도 적용되어야 할 경우 react는 상태끌어올리기를 통해 해결할 수 있다.

1. 상태 설정 함수 props로 내리기

## 상태 끌어올리기
### state 공유
<p align='center'><img src='../image/Components%202.png' width=600/></p>

위와 같은 컴포넌트들이 구성되어 있다고 했을 때,<br>
AddTweetForm은 새로운 tweet을 만들고, Tweets는 Tweet을 목록의 형태로 구성한다. 서로 연결되어 있지 않은 AddTweetForm과 Tweets, 두 컴포넌트를 통해 Twitter가 온전히 작동하려면 AddTweetForm의 동작 결과가 Tweets에 적용이 되어야 한다.

하지만 AddTweetForm에서 새로운 tweet을 만들수 있는 데이터를 가지고 있는다 해도 react는 단방향(하향식) 데이터흐름을 가지기 때문에 그 데이터를 형제 컴포넌트인 Tweets에 전달해 트윗 목록에 추가할 수 없다.

이렇게 서로 부모 자식 관계를 가지지 않는 컴포넌트가 같은 데이터를 공유해야 하는 경우에는 아래의 그림처럼 가장 가까운 부모 컴포넌트에 그 데이터를 지정해 나눌 수 있다

<p align='center'><img src='../image/Components%201.png' width=600/></p>

위 상황과 같이 상위 컴포넌트에 가변적 성질을 가지며, 공유해야할 데이터를 state로 만들어 놓는다면<br>
다음으로 고려해야할 부분은 하위 컴포넌트에서 어떻게 상위 컴포넌트의 state를 변형시켜야 하는가 이다.

Twitter 컴포넌트에서 변형할 수도 있지만 AddTweetForm이 가지는 기능을 재사용해야 한다면, 결과적으로 같은 기능을 가지는 똑같은 코드가 여러곳에 너무도 많아질 것이다.<br>
나아가 이런 상황은 결국 유지보수에 있어서 걸림돌이 될 것이다.

### prop, EventHandler
1. 하위 컴포넌트는 상위 컴포넌트로 부터 props를 통해 객체 형태로 데이터를 전달 받는다. <br>
(props는 읽기 전용 객체로 react의 모든 컴포넌트는 props를 다룰 때 입력값을 바꾸려하지 않는 순수 함수처럼 동작해야 한다.)
2. props가 객체라는 말은 결국 props안에 함수를 포함시킬 수 있다는 뜻이 된다.

1, 2번의 규칙을 종합하면 props는 상위 컴포넌트로부터 받아오는 객체(`함수를 포함한 다양한 type의 값을 가지는`) 이며, 그 값을 그대로 사용해야 한다는 뜻이 된다.

이를 해결해야할 문제와 연관지어본다면,
1. 하위 컴포넌트에서 상위 컴포넌트의 state를 수정해 복수의 컴포넌트를 수정해야한다.
2. 하위 컴포넌트는 상위 컴포넌트로부터 함수를 받아올 수 있다.

즉, 하위 컴포넌트에 상태 갱신 함수를 가져올 수 있다는 뜻이 된다.

<p align='center'><img src='../image/Components%203.png' width=600/></p>

하지만 함수를 props로 아무런 조치 없이 내려 보낸다면, 참조값 문제로 하위컴포넌트에 무의미한 렌더링이 일어날 수도 있기 때문에 Memoization을 통해 함수의 동일성을 확보해 주어야 한다.

상태 갱신 함수에 대한 Memoization 필요성 여부는 react 문서를 확인해 보면 알 수 있다.
>주의<br>
React는 setState 함수 동일성이 안정적이고 리렌더링 시에도 변경되지 않을 것이라는 것을 보장합니다. 이것이 useEffect나 useCallback 의존성 목록에 이 함수를 포함하지 않아도 무방한 이유입니다.

useState의 상태 갱신 함수는 동일성이 보장되기에 특별한 조치 없이 Event Handler를 통해 전달이 가능하다.

위 그림을 다음과 같이 코드로 변환할 수 있다.

* Twitter.js
	
	```jsx
	import React, { useState } from "react";
	import AddTweetForm from './Page/AddTweetForm'
	import Tweets from './Page/Tweets'

	function Twittler() {
		const [tweets, setTweets] = useState([
			{
				id: 1,
				username: 'sam',
				createdAt: '2021-11-23',
				content: 'hello'
			},
			{
				id: 2,
				username: 'mark',
				createdAt: '2021-12-03',
				content: 'hi'
			}
			]);

		const addNewTweet = (newTweet) => {
			setTweets([newTweet, ...tweets]);
		}; // 전달할 handler 함수 setState는 콜백함수로 보낸다.

		return (
			<div>
				<div>name: sam</div>
				<AddTweetForm addNewTweet={addNewTweet}/>
				<Tweets tweets={tweets}/>
			</div>
		);
	}

	export default Twittler;
	```

* AddTweetForm
	
	```jsx
	import React, { useState } from "react"

	function AddTweetForm({ addNewTweet }) {
		const [tweetContent, setTweetContent] = useState("");
		const [name, setName] = useState('')

		const onTextChange = (e) => {
			setTweetContent(e.target.value);
		};
		
		const onNameChange = (e) => {
			setName(e.target.value);
		}

		let uuid4 = self.crypto.randomUUID();
		
		onst onClickSubmit = () => {
			let newTweet = {
				id: uuid4,
				username: name,
				createAt: new Date().toISOString(),
				content: tweetContent
			};
			addNewTweet(newTweet); // handler를 호출하면 setState가 실행 => Twitter의 state 변경
		};

		return (
			<div>
				<input
				 type='text'
				 value={name}
				 onChange={onNameChange}
				 placeholder='...please write your name'></input>
				<textarea value={tweetContent} onChange={onTextChange}></textarea>
				<button onClick={onClickSubmit}>
					새 글 쓰기
				</button>
			</div>
		);
	}

	export default AddTweetForm;
	```

* Tweets
	
	```jsx
	import React, { useState } from "react"
	import Tweet from '../Component/Tweet'

	function Tweets({tweets}) { // Twitter의 state가 변경되면 props로 오는 값도 변경 => 리렌더링+목록 개수 증가
		return (
			<ul id="tweets">
				{tweets.map((t) => (
					<Tweet key={t.id} username={t.username} createdAt={t.createdAt}>
						{t.content}
					</Tweet>
				))}
			</ul>
		)
	}

	export default Tweets;
	```

### 결론
특정 컴포넌트에서의 데이터 변화가 다른 컴포넌트에 적용되어야 하는 경우 가장 가까운 상위 컴포넌트에 소스 데이터를 위치시키고, 상태 갱신 함수를 props로 내려받아 데이터를 수정한다.
___
**참고 사이트**
>React-문서-State 끌어올리기
* https://ko.reactjs.org/docs/lifting-state-up.html