>HTTP Method의 특징에 대해 정리한다

## HTTP 프로토콜에서의 요청 메서드
클라이언트가 웹서버에게 요청하는 목적 및 그 종류를 알리는 수단으로 HTTP Message의 start line(맨 첫째줄에) 위치한다.

## 대표적인 메서드
### GET
* 서버에서 resource를 보내도록 요청하는데 사용한다.
* 서버의 resource를 읽는다.
### HEAD
* GET과 동일하지만 서버에서 body를 반환하지 않는다.	
* 용도
  * resource를 받지 않고 오직 찾기만 원할 때
  * object가 존재할 경우 응답의 상태 코드를 확인할 때
  * 서버의 응답 헤더를 봄으로써 resource가 수정되었는지 확인
### PUT
* 서버에 문서를 쓸 때 사용하며, GET과 반대되는 기능을 한다.
* 특징
	* PUT 메서드는 서버가 client 요청의 body를 확인한다
	* 요청된 url에 정의된 새로운 resource를 생성하기 위해 사용한다,
	* 요청된 url이 존재할 경우 기존 resource를 대체한다.
### POST
* 서버에 입력 데이터를 보내기 위해 사용하며, 주로 HTML.form에 많이 사용된다.
* PUT과 POST의 차이
	* PUT은 서버의 resource에 data를 저장하기 위한 용도로 사용된다.
	* POST는 서버에 data를 보내기 위한 용도로 사용된다.
### TRACE
* 클라이언트로 부터 request packet이 방화벽, proxy server gateway등을 거치면서 packet의 변조가 일어날 수 있는데, 이 때 서버에 도달했을 때의 최종 packet의 request	 packet을 볼 수 있다.
* 특징
	* Original Data와 서버에 도달했을 때의 비교본 Data를 서버의 응답 Body를 통해 확인 할 수 있다.
	* 요청의 최종 수신자는 반드시 송신자에게 200(OK) 응답의 내용(Body)로 수신한 메세지를 반송해야 한다
	* 최초 Client의 요청에는 Body가 포함될수 없다.
### OPTIONS
* arget Server의 지원가능한 method(ex>GET, POST…)를 알아보기 위해 사용된다.
* 주로 CORS의 Preflight request에서 사용된다.
### DELETE
* 요청 resource를 삭제하도록 요청한다.
* 특징
	* 리소스 안정성의 문제로 HTTP 규격에는 클라이언트의 요청에도 서버가 삭제를 무효화 시킬수 있도록 정의되어 있음
	* DELETE는 항상 보장되지는 않는다.
### GET과 POST의 차이
* GET: GET을 통한 요청은 url 주소 끝에 파라미터(query string)로 포함되어 전송된다.
	* 캐시 가능:<br>
	GET을 통해 서버에 resource를 요청할 때 웹 캐시가 요청을 가로채 서버로부터 resource를 다시 다운로드 하는 대신 resource의 복사본을 반환한다. (HTTP 헤더에서 cache-control 헤더를 통해 캐시 옵션을 지정할 수 있다.)
	* browser history에 남는다.
	* 북마크 될 수 있다.
	* 요청에 길이 제한이 있다.<br>
	브라우저마다 제한하는 요청 길이가 존재한다.
	* 중요한 정보를 다루면 안된다.<br>
	요청이 파라미터에 다 노출되어 버리기 때문에 민감한 정도가 노출될 수 있다.
	* 데이터를 요청할때만 사용된다.
* POST: POST는 전송할 데이터를 HTTP 메시지 body에 담아서 서버로 보낸다. 때문에 길이 제한이 따로 없어 용량이 큰 데이터를 보낼 수 있으며, 외부로 노출되지 않아 데이터 암호화와 함께 사용해 중요한 데이터를 보내기도 한다.
	* 캐시되지 않는다.
	* 브라우저 히스토리에 남지 않는다.
	* 북마크 되지 않는다.
	* 데이터 길이에 제한이 없다.

사용 목적의 차이:<br> GET은 서버의 resource에서 데이터를 요청할 때, POST는 서버의 resource를 새로 생성하거나 업데이트할 때 사용한다.

요청에 body 유무:<br>
GET 은 URL 파라미터에 요청하는 데이터를 담아 보내기 때문에 HTTP 메시지에 body가 없다. POST 는 body 에 데이터를 담아 보내기 때문에 HTTP 메시지에 body가 존재한다.

멱등성(idempotent): GET 요청은 멱등이며, POST는 멱등이 아니다.

이외에도 더 많은 메서드가 존재하지만 대부분의 웹 서버가 보안상의 이유로 GET, POST 2개 또는 OPTIONS를 포함해 3개만을 허용하는 경우가 일반적이다.

___
**참고 사이트**

>정보통신기술용어해설-HTTP Request Method, HTTP Request Type   HTTP 요청 메소드, HTTP 요청 종류
* http://www.ktword.co.kr/test/view/view.php?no=3791

>tistory-인생의 로그캣-[네트워크] get과 post의 차이
* https://noahlogs.tistory.com/35

>W3sschools-HTML Reference-HTTP Request Methods
* https://www.w3schools.com/tags/ref_httpmethods.asp