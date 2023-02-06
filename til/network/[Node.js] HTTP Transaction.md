>Node.js에서 HTTP 요청과 응답을 어떻게 처리하는지 알아본다.

### 서버 생성
모든 node 웹 서버 애플리케이션은 `createServer`를 사용해 웹 서버 객체를 만들어야 한다.
```javascript
const http = require('http');

const server = http.createServer((request, response) => {
  // 여기서 작업이 진행
});
```
서버로 오는 HTTP요청마다 createServer에 전달된 함수가 한번씩 호출된다.<br>
createServer를 통해 생성된 server 객체는 EventEmitter 객체를 기반으로 만들어 졌다. 그래서 EventEmiiter의 메서드를 상속 받는다. 위의 코드와 아래의 코드는 똑같이 동작한다.
```javascript
const http = require('http')
const server = http.createServer();
server.on('request', (request, response) => {
  // 여기서 작업이 진행
});
```

### 메서드, url, 헤더
request 객체에는 method, url, headers 프로퍼티가 존재한다.
* method: HTTP 메서드
* url: 전체 url에서 서버, 프로토콜, 포트를 제외한 나머지
* headers: HTTP message header

### request body
핸들러에 전달된 request객체는 ReadableStream 인터페이스를 구현하고 있는 EventEmitter를 상속하는 객체다. 이 스트림에 리스너를 등록하거나(`.on()`) 다른 스트림에 파이프로 연결할 수 있다.(`.pipe()`)
```javascript
let body = [];
request.on('data', (chunk) => {
  body.push(chunk);
}).on('end', () => {
  body = Buffer.concat(body).toString();
  // 여기서 `body`에 전체 요청 바디가 문자열로 담겨있다.
});
```
'data' 이벤트에서 발생한 chunk(데이터 덩어리)는 Buffer다.
#### Buffer
버퍼란 Node.js에서 제공하는 Binary의 데이터를 답을 수 있는 Object를 말한다.<br>
프로그래밍에서는 거의 대부분 CPU와 보조기억장치 사이에서 사용되는 임시저장 공간을 의미한다. CUP연산에 비해 느린 보조기억장치에서 자신보다는 빠른 주 이거장치(RAM)에 데이터를 쌓아두고, CPU가 다른 연산을 처리하다가 한번씩 버퍼를 확인해 버퍼가 다 차면 한꺼번에 처리한다.

Buffer 메서드
* .concat(arr) : 배열 안에든 버퍼들을 하나로 결합
* .from(str) : 문자열에서 버퍼로 변경
* .toString(buf) : 버퍼에서 문자열로 변경


### 요류 처리
request객체는 오류가 발생했을 때 EventEmitter 처럼 동작한다.

request 스트림의 오류가 발생하면 스트림에서 'error' 이벤트가 발생하면서 오류를 전달한다. 이벤트 리스너가 등록되어 있지 않다면 Node.js 프로그램을 종료시킬 슈도 있는 오류를 던진다. 그러므로 단순히 오류를 로깅만 하더라도 요청 스트림에 error리스너를 추가해야 한다.(HTTP 오류 응답을 보내는 것이 좋다.)
```javascript
request.on('error', (err) => {
  // 여기서 `stderr`에 오류 메시지와 스택 트레이스를 출력
  console.error(err.stack);
});
```

위의 내용을 코드로 모으면 다음과 같다
```javascript
const http = require('http');

http.createServer((request, response) => {
  const { headers, method, url } = request;
  let body = [];
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {
    body = Buffer.concat(body).toString();
    // 여기서 헤더, 메서드, url, 바디를 가지고
    // 이 요청에 응답하는 데 필요한 어떤 일이라도 할 수 있다.
  });
}).listen(8080); // 이 서버를 활성화하고 8080 포트로 받습니다.
```

### response header, body
response 객체는 ServerResponese의 인스턴스이면서 WritableStream이다. 여기에는 클라이언트 데이터를 응답하기 위한 여러 메서드가 존재한다.
* setHeader(json) : 헤더를 설정한다.
	```javascript
	response.setHeader('Content-Type', 'application/json');
	response.setHeader('X-Powered-By', 'bacon');
	```
* writeHead(port, json) : 스트림에 상태 코드와 헤더를 작성해 명시적으로 전송
	```javascript
	response.writeHead(200, {
  	'Content-Type': 'application/json',
  	'X-Powered-By': 'bacon'
	});
	```
* write() : 전송할 body를 작성
* end() : body를 전달
	```javascript
	response.write('<html>');
	response.write('<body>');
	response.write('<h1>Hello, World!</h1>');
	response.write('</body>');
	response.write('</html>');
	response.end();
	/// 축약하면
	response.end('<html><body><h1>Hello, World!</h1></body></html>');
	```

위의 내용들을 모아보면 다음과 같다
```javascript
const http = require('http');

http.createServer((request, response) => {
  const { headers, method, url } = request;
  let body = [];
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {
    body = Buffer.concat(body).toString();
    response.on('error', (err) => {
      console.error(err);
    });

    response.statusCode = 200;
    response.setHeader('Content-Type', 'application/json');
    // 주의: 위 두 줄은 다음 한 줄로 대체가능
    // response.writeHead(200, {'Content-Type': 'application/json'})

    const responseBody = { headers, method, url, body };

    response.write(JSON.stringify(responseBody));
    response.end();
    // 주의: 위 두 줄은 다음 한 줄로 대체가능
    // response.end(JSON.stringify(responseBody))
  });
}).listen(8080);
```

___
**참고 사이트**
>naver blog-뽕-[Node.js] Buffers - 사용하기
* https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=kbh3983&logNo=220865868571

>tistory-코끼리를 냉장고에 넣는 방법-DOLOLAK-[개념정리] 버퍼(BUFFER)란? 버퍼 개념
* https://dololak.tistory.com/84

>Node.js-문서-HTTP 트랜잭션 해부
* https://nodejs.org/ko/docs/guides/anatomy-of-an-http-transaction/

>Tistory-stickie-[Node.js] 이벤트(Event) 이해하기
* https://stickie.tistory.com/66

>GitHub-Web Developer’s Blog-YONGFEOK AN-Buffer 이해하기 01
* https://krauser085.github.io/node.js-01-Buffer/

>tistory-Inpa Dev-인파-[NODE] 📚 버퍼 / 스트림 / 파이프 문법 💯 정리
* https://inpa.tistory.com/entry/NODE-%F0%9F%93%9A-%EB%B2%84%ED%8D%BC-%EC%8A%A4%ED%8A%B8%EB%A6%BC-%ED%8C%8C%EC%9D%B4%ED%94%84-%EC%A0%95%EB%A6%AC