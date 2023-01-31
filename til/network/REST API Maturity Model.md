>REST API를 성숙도 모델(Maturity Model) 중심으로 정리한다. REST API 혹은 RESTful에 대한 내용은 다른 파일에서 정리한다.
## REST API (Representational State Transfer)
로이 필딩(Roy Fielding)이 제시한 웹(http)의 장접을 최대한 활용할 수 있는 아키텍처

웹에서 사용되는 데이터나 자원(Resource)을 HTTP URL로 표현하고, HTTP 프로토콜을 통해 요청과 응답을 정의하는 방식

### REST API를 디자인하는 방법
리차든슨(Leonard Richardson)이 제시한 REST API를 잘 적용하기 위한 4단계 모델

<img src='../javascript/image/rest%20api%20level.png' width=500/>

* 0단계

	0단계는 단순히 HTTP 프로토콜을 사용하는 것만으로도 충족된다.

	하나의 엔드포인트(End Point)를 사용해 매개변수를, 통해서만 여러 동작을 하게 된다.
	
	<img src='../javascript/image/level%200.png' width=600/>

* 1단계

	모든 자원은 개별 리소스에 맞는 엔드포인트를 사용해야하며, 요청하고 받는 자원에 대한 정보를 응답으로 전달해야 한다.

	응답에 성공/실패 여부가 포함되어야 한다.
	
	<img src='../javascript/image/level%201.png' width=600/>
	
	요청의 용도에 따라서 엔드포인트가 선택적으로 바뀌는 모습을 볼 수 있다. (`doctors/Richard`, `slots/52`)

* 2단계

	CRUD에 맞게 적절한 HTTP 메서드를 사용하는 것에 중점을 둔다.

	이전 단계에서는 모든 요청을 POST하나(혹은 GET을 대신 사용하거나 추가로 사용, 큰 차이는 없다)만 사용 했지만 2단계에서는 HTTP 메서드를 최대한으로 사용한다.
	
	<img src='../javascript/image/level%202.png' width=600/>
	
	* GET : 서버의 데이터를 변화시키지 않는 요청에 사용
	* POST : 요청마다 새로운 리소스 생성
	* PUT : 요청마다 같은 리소스 반환 (용도 = 교체)
	* PATCH : 요청마다 같은 리소스 반환 (용도 = 수정)

	요청마다 같은 리소스를 반환하는 특징을 **멱등**(idempotent)하다고 한다.
	
	REST 성숙도 모델의 2단계까지 적용하면 대체적으로 잘 작성된 API라고 한다
	
	로이 필딩은 3단계까지 만족하지 못한 API는 HTTP API라고 불러야한다고 주장하지만, 3단계까지 만족하는 경우는 드물다.

* 3단계

	HATEOAS(Hypermedia As The Engine Of Application State)라는 하이퍼미디어 컨트롤을 적용한다.

	응답을 받은 다음에 할 수 있는 다양한 액션들을 위해 많은 하이퍼미디어 컨트롤을 포함한다.

	<img src='../javascript/image/level%203.png' width=600/>

	요청은 2단계와 동일하지만, 응답에는 URI를 포함한 링크 요소를 삽입해 새로운 기능에 접근할 수 있도록 한다.
	
	HATEOAS 외에 추가적으로 메시지가 클라이언트에게 자신을 스스로 설명해야 한다는 self-descriptive도 있다.

### Open API, API Key
* Open API

	공공데이터 포털에서와 같이 누구에게나 열려있는 API

	하지만 무제한으로 이용할 수 있지는 않다. API마다 정해진 이용 수칙이 있고, 그 이용 수칙에 따라 제한사항(가격, 정보의 제한 등)이 있을 수 있다.

* API Key

	API를 이용하기 위한 Key

	API Key는 서버의 문을 여는 열쇠로, 서버를 운용하는데에 비용이 발생하기 때문에 로그인한 이용자에 한해 자원에 접근할 수 있는 권한의 API Key의 형태로 제공하고, 사용자는 데이터를 요청할 때 key를 같이 전달해야 한다.

	___
	**참고 사이트**
	>코드 스테이츠
	
	>martinFowler.com-Martin Fowler-Richardson Maturity Model
	* https://martinfowler.com/articles/richardsonMaturityModel.html