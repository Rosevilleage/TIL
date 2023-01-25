>fetch()와 json()에 대해 알아본다.

## Fetch API
Fetch API는 HTTP 파이프라인을 구성하는 요청과 응답 등의 요소를 자바스크립트에서 접근하고 조작할 수 있는 인터페이스를 제공한다.  <br>
fetch()를 통해 네트워크의 리소스를 쉽게 비동기적으로 가져올 수 있다.

* Jquery.ajax()와의 차이

	1. fetch()가 반환하는 promise 객체는 404, 500 같은 HTTP오류 상태를 수신해도 거부되지 않는다.
		
		fetch()의 promise 객체는 서버에서 헤더를 포함한 응답을 받는 순간 정상적으로 이행한다.(대신 응답의 상태가 200-299를 벗어날 경우 Response.ok 속성이 false로 설정) <br> promise가 거부되는 경우는 네트워크 연결이 실패하는 겨우를 포함, 아예 요청을 완료하지 못한 경우로 한정된다.

		HTTP오류를 확인 하려면 Response.ok 혹은 Response.status 속성을 용해야 한다.
	2. 자격 증명(credentials) 옵션을 제공하지 않은 경우, fetch()는 **교차 출처 쿠키를 전송하지 않는다.**

### Response
Fetch API의 Response 인터페이스는 요청에 대한 응답을 나타낸다.

Response() 생성자를 사용해 새 Response 객체를 생성할 수 있지만, 다른 API작업(예: 서비스 작업자 FetchEvent.respondWith 혹은 간단한 fetch())의 결과로 반환되는 Response 객체를 사용할 가능성이 높다. 

### Request
Fetch API의 Request 인터페이스는 리소스 요청을 나타낸다.

Request() 생성자를 사용하여 새 Request 객체를 생성할 수 있지만, 서비스 작업자 FetchEvent.request와 같은 다른 API 작업의 결과로 반환되는 Request 객체를 사용할 가능성이 높다.

### fetch()
fetch()는 네트워크에서 리소스를 가져오는 프로세스를 시작하고 응답을 사용할 수 있게 되면 이행되는 promise를 반환한다.	promise는 요청에 대한 응답을 나타내는 Response 객체로 확인된다.
```javascript
const url = //가져오려는 네트워크 요청
fetch(url)
	.then(response=>response.json())
	.then(data=>console.log(data))
```
**fetch()의 대표적인 2 가지 매개변수**
1. resource
	* 가져오려는 리소스의 URL을 제공하는 URL객체 또는 문자열
	* Request 객체
2. options
	
	요청에 적용할 사용자 정의 설정을 포함하는 객체로 사용 가능함 옵션을 3 가지가 있다.
	* method : GET(기본값), POST, PUT, DELETE
	* headers : Headers 객체 또는 String값이 있는 객체 리터럴에 포함된 요청에 포함하려는 모든 헤더 (일부 이름은 금지되어 있음)
	* body : 요청에 추가하려는 본문
	* mode : 요청에 사용할 모드(예: cors, no-cors 또는 same-origin)

```javascript
fetch(resource)
fetch(resource, options)
```

### json()
json()은 이름만 봐서는 JSON객체로 변환해 줄것 같지만 그렇지 않다.

Response 인터페이스의 json() 메서드는 Response 스트림을 가져와서 끝까지 읽는다. 본문 텍스트를 JSON으로 구문 분석한 결과로 해결되는 약속을 반환한다.

이름이 json()임에도 불구하고 분석 결과는 JSON(데이터 교환을 위한 데이터 포멧[표준 자료 구조])이 아니라 JSON을 입력으로 사용하고 이를 구문 분석하여 생성한 JavaScript 객체다.
```javascript
const url = // 가져오려는 네트워크 요청

fetch(url) // 요청에 대한 응답을 나타내는 Response객체를 결과값으로 하는 Promise 객체를 반환
	.then(response=>response.json()) // Reponse객체를 JSON으로 분석한 Js객체를 결과값으로 하는 Promise객체를 반환
	.then(data=>console.log(data)) // {...(데이터)}
	.then(data=>console.log(JSON.parse(data))) // Unexpected token o in JSON at position 1 || data가 JSON이 아니라 javascript 객체이기 떄문에 오류가 발생한다.
```

___
**참고 사이트**

>MDN-Fetch API
* https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API

>MDN-Request
* https://developer.mozilla.org/en-US/docs/Web/API/Request

>MDN-Response
* https://developer.mozilla.org/en-US/docs/Web/API/Response

>MDN-Response.json()
* https://developer.mozilla.org/en-US/docs/Web/API/Response/json

>MDN-fetch()
* https://developer.mozilla.org/en-US/docs/Web/API/fetch