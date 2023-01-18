>자바스크립트가 비동기적으로 움직일 수 있는 이유에 대해 알아본다.

## 자바스트립트의 싱글 스레드
자바스크립트는 싱글 스레드(single thread) 기반의 언어이다. 이는 자바스크립트는 한번에 한가지 명령만 처리할 수 있다는 뜻이 된다
### 자바스크립트의 엔진 V8
자바스크립트 엔진인 V8은 콜스택과 힙을 가지고 있다.
* **콜 스택 (call stack)** : 후입선출(LIFO: last in first out) 방식의 자료구조
	* 자바스크립트 코드 실행시 생성되는 실행 컨텍스트를 저장한다. 함수가 호출되면 콜스택에 추가된다.
	* 함수 실행이 완료되면, 스택에서 제거한다. 
* **힙 (heap)** : 객체가 할당되는 메모리 공간, 단순히 메모리의 큰(대부분 구조화되지 않은) 영역을 지칭

이 구조적 한계 때문에 당연히 자바스크립트는 비동기적 작동이 불가능해야 하지만 그렇지 않다. 왜냐하면 자바스크립트는 런타임 환경에서 `Node`나 `웹 브라우저`를 사용해 멀티 스레드(multi thread)로 작동할 수 있기 때문이다.

### 런타임 환경 (web browser)
브라우저는 web API, task queue, event loop를 가지고 있다.
* **Web API** :
	
	브라우저에서 제공하는 API(DOM/AJAX/Timeout 등), 스택으로부터 비동기 함수 호출을 받고 연산하고 완료되면, task queue로 이동 시킨다.
* **Task queue** :

	 선입선출(FIFO: first in first out) 방식의 자료구조(진짜 queue(자료구조)는 아니다. 실제로는 set으로 구현되어 있음) web api로부터 받은 호출을 대기 시킨다. <br>
받은 호출의 종류에 따라 안에서 나눈다.
	* **Tast queue(Macrotask queue)** : setTimeout(), setInterval(), setImmdiated() 같은 task를 넘겨 받는다.
	* **Microtask queue** : Promise나 async/await, process.nextTick, Object.observe, MultationObserver 같은 비동기 호출을 넘겨 받는다. 우선순위는 Macrotask보다 높다.
	* **Animation frames** : requestAnimationFrame 같이 브라우저 렌더링과 관련된 task를 넘겨 받는다. 우선순위는 Microtask보다 낮고, Macrotask 보다 높다.
* **Event loop** :

	큐스택이 비어있는지 상시 확인하며, 비어있는게 확인되면 우선순위에 따라 task queue로부터 호출을 가져와 큐 스택에 추가한다. 
<p align='center'>
<img src='image/visualized%20event%20loop.png' width=800/>
</p>
___
MDN-이벤트 루프
* https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop

MDN-Wev API
* https://developer.mozilla.org/ko/docs/Web/API

velog-titu-[JavaScript] Task Queue말고 다른 큐가 더 있다고? (MicroTask Queue, Animation Frames)
* https://velog.io/@titu/JavaScript-Task-Queue%EB%A7%90%EA%B3%A0-%EB%8B%A4%EB%A5%B8-%ED%81%90%EA%B0%80-%EB%8D%94-%EC%9E%88%EB%8B%A4%EA%B3%A0-MicroTask-Queue-Animation-Frames-Render-Queue

DEV-Lydia Hallie-Javascript Visualized Event Loop
* https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif