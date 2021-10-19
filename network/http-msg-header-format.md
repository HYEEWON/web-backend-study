# 🔗 HTTP Request/Reponse Header 포멧

<br>

## 🌈 HTTP 공통 헤더

### 👉 Cache-Control
* 캐싱 정책을 정하는 헤더
* 예시
  * Cache-Control: no-store (캐시 서버에 컨텐츠를 저장하지 않음)
  * Cache-Control: no-cache (캐시를 사용하기 전에 서버에 캐시를 사용해도 되는지 재검증 요청)
  * Cache-Control: public, max-age=3600 (공유 캐시에 저장, 캐시의 유효시간은 3600초)

### 👉 Connection
* 클라이언트와 서버의 연결과 관한 정보를 지정하는 헤더
* 예시
  * Connection: keep-Alive (현재 TCP 연결을 유지)

### 👉 Date
* 메세지가 생성된 시간 정보
* 자동으로 생성됨

### 👉 Pragma
* HTTP/1.1 이전에 사용, HTTP/1.1의 Cache-Control과 동일한 역할을 하는 헤더
* 예시) 
  * Pragma: no-cache (Cache-Control: no-cache와 같음)
    * 캐시 서버에 컨텐츠가 있어도 실제 웹 서버에서 컨텐츠를 받음

### 👉 Via
* 프록시 서버의 정보를 알려줄 때 사용하는 헤더
* 요청이 거처간 프록시 서버의 경로를 알수 있음

### 👉 Transfer-Encoding
* 컨텐츠를 분할하여 전송하는 Chuncked 전송 방식을 이용할 때, 주로 사용되는 헤더
* 서버에서 전송하고자 하는 자원의 크기가 크면 이를 분할하여 전송함
* 자원을 한 번에 처리할 때는 Content-Length를 이용하지만 분할하여 전송할 때에는 Transfer-Encoding을 이용함
* 예시
  * Transfer-Encoding: Chunked

### 👉 Upgrade
* 클라이언트와 서버의 통신 프로토콜을 바꿀 때, 사용하는 헤더
* 새로운 HTTP 버전을 사용하거나 암호화 프로토콜을 변경하는 경우

### 👉 Warning
* 케시 서버의 문제를 클라이언트에게 알리는 용도로 사용되는 헤더

### 👉 Trailer
* 서버가 Chuncked 전송 방식을 이용할 때, 사용할 수 있는 헤더
* 메시지의 Body 끝 부분에 올 HTTP Header를 지정
* 예시
  * Trailer: Expires (메시지의 Body 끝 부분에 Expires 헤더의 내용이 추가)

<br>

## 🌈 HTTP Request 헤더

### 👉 Accept
* 클라이언트가 응답으로 받을 수 있는 미디어 타입을 지정하는 것에 사용
* 예시
  * Accept: */* (type / sub type)

### 👉 Accept-Charset
* 클라이언트가 응답으로 받을 수 있는 문자 집합(문자 인코딩)을 지정하는 것에 사용
* 예시
  * Accept-Charset: utf-8

### 👉 Accept-Encoding
* 클라이언트가 응답으로 받을 수 있는 압축방식을 지정하는 것에 사용
* 예시
  * Accept-Encoding: * (compress, gzip 등)

### 👉 Accept-Language
* 클라이언트가 응답으로 받기를 원하는 언어를 선택할 때 사용
* 예시
  * Accept-Language: ko, en-US

### 👉 Authorization
* JWT, Bearer 등 인증 토큰을 서버로 전송할 때 사용
* 예시
  * Authorization: 토큰-종류 실제-토큰-문자

### 👉 Expect
* 클라이언트에 의해 서버에서 특정 작업이 요구될 때, 그 작업을 나타내기 위해 사용
* 서버가 기대 값을 만족시킬 수 없다면 4XX 에러 발생

### 👉 From
* 클라이언트의 이메일 주소

### 👉 Host
* 서버의 도메인이름과 포트를 지정
* 요청에는 반드시 하나의 Host 필드가 존재해야 함

### 👉 User-Agent
* 클라이언트의 정보(브라우저, OS 등)를 포함
* 통계를 목적으로 사용

### 👉 Referer
* 현재 페이지 이전의 페이지 주소 정보
* 어떤 페이지를 거쳐 지금의 페이지로 왔는지 알 수 있음

### 👉 If-Modified-Since
* 제시된 시간 이후에 파일 변경 → 서버는 파일을 반환하고 `200 OK`로 응답
* 제시된 시간 이후에 파일이 변경되지 않음 → 서버는 파일을 반환하지 않고 `304 Not Modified`로 응답(클라이언트는 캐시에서 파일을 가져옴)

### 👉 If-Unmodified-Since
* 제시된 시간 이후에 파일 변경 → 서버는 요청을 수행하지 않고 412 Precondition Failed로 응답
* 제시된 시간 이후에 파일이 변경되지 않음 → 서버는 파일을 반환하고 200 OK로 응답

### 👉 If-Match
* 서버에 존재하는 리소스의 ETag가 요청의 If-Match의 값과 일치하면 200 OK로 응답
* 서버에 존재하는 리소스의 ETag가 요청의 If-Match의 값과 다르면 412 Precondition Failed로 응답

### 👉 If-None-Match
* 서버에 존재하는 리소스의 ETag가 요청의 If-None-Match의 값과 다르면 200 OK로 응답
* 서버에 존재하는 리소스의 ETag가 요청의 If-None-Match의 값과 일치하면 304 Not Modified로 응답
* If-Modified-Since는 시간 정보를 이용해 정확성이 보장되지 않을 수 있으나, If-None-Match는 ETag 정보를 이용해 리소스 변경 여부를 정확하게 알 수 있음

### 👉 Range
* 컨텐츠의 일부를 요청할 경우, 그 범위를 지정하기 위해 사용하는 헤더
* 특정 파일을 다운받다가 오류가 발생하여 다시 요청하여 다운받는 경우 등에 사용
* 웹서버는 206 Partial Content로 응답

### 👉 If-Range
* 캐시와 관련된 헤더로 요청하는 자원의 ETag 정보를 나타냄
* 특정 자원의 일부를 요청한 뒤, 일정 시간이후에 다시 요청할 경우에 해당 자원이 여전히 존재하는지 확인하기 위해 사용
* 클라이언트는 IF-Range와 Range 헤더를 지정하여 요청 → 자원이 변경 → 206 Partial Content로 응답, Range에 해당하는 자원만 반환
* 클라이언트는 IF-Range와 Range 헤더를 지정하여 요청 → 자원이 변경되지 않음 → 200 OK로 응답, 새로운 자원 전체를 반환

### 👉 Max-Forwards
* 요청이 여러 프록시 서버를 거칠 때, 네트워크 오류의 원인을 찾는데 도움을 줄 수 있는 헤더
* Max-Forwards 헤더의 값은 정수로, 프록시 서버를 거치며 1씩 감소 → 값이 0인 메시지를 받은 프록시 서버가 응답

### 👉 Proxy-Authorization
* 포워드 프록시 환경에서 사용되는 헤더
* 프록시 서버
* 포워드 프록시: 클라이언트의 인터넷 접속 보안
* 리버스 프록시: 웹 서버 성능 향상

### 👉 TE
* 클라이언트가 지원하는 Transfer-Encoding(전송 인코딩) 방식을 서버에게 알리기 위해 사용
* 예시
  * TE: Trailers (Chucked 방식을 지원)

<br>

## 🌈 HTTP Response 헤더

### 👉 Location
* 응답 코드가 3XX, 201일 경우에 볼 수 있는 헤더
* 요청된 자원의 위치를 알려줌 (리다이렉트)

### 👉 Age
캐시 응답에서 나타는 헤더
* max-age 시간에서 시간이 얼마나 흘렀는지를 초 단위로 알려줌

### 👉 ETag
* 자원이 변경되었는지를 검사할 수 있는 헤더
* 자원의 식별 변호

### 👉 Server
* 서버의 정보를 나타내는 헤더 (HTTP SW 버전 정보)

### 👉 Accept-Ranges
* 서버가 클라이언트에게 요청의 Range 헤더를 처라할 수 있는지 알려주는 헤더
* 예시
  * Accept-Range: bytes (Range 처리 가능)
  * Accept-Range: none (Range 처리 불가)

### 👉 Vary
* 캐시서버에게 캐싱정책을 가이드할 수 있는 헤더
* User-Agent 등 HTTP 요청의 헤더 이름을 사용
* 예시
  * Vary: User-Agent (캐싱하고 있는 컨텐츠의 최초 User-Agent 정보가 일치하지 않으면 캐싱된 컨텐츠를 전달하지 않음)

### 👉 Retry-After
* 서버가 클라이언트에게 재 접속 메시지를 전달할 때 사용하는 헤더
* 웹 브라우저(IE, Chrome, MS Edge 등)는 Retry-After 헤더를 무시

### 👉 WWW-Authenticate
* 허가된 클라이언트에게만 컨텐츠를 제공해야 할 때 사용
* 서버가 클라이언트를 인증할 경우에 사용

### 👉 Proxy-Authenticate
* 허가된 클라이언트에게만 컨텐츠를 제공해야 할 때 사용
* 프록시 서버가 클라이언트를 인증할 경우에 사용

<br>

## 🌈 X-Forwarded-For (XFF)

### 👉 정의
* HTTP Header 중 하나로 HTTP Server에 요청한 Client의 IP를 식별하기 위한 표준
* 웹 서버 앞에 Load balancer, Proxy 서버, Cashing 서버 등이 있는 경우 웹 서버는 서버에 접근한 주체를 Proxy 서버와 같은 장비로 인식을 하게 되는데, XFF를 사용하면 실제 클라이언트의 IP주소를 알 수 있음
* "X-"로 시작하는 헤더
  * 예전에 사용자가 임의로 정의한 헤더를 사용할 때, "X-"를 앞에 추가, 2012년 이후로는 사용자 정의 헤더에 "X-"를 붙이지 않아도 됨
  * 하지만 많이 사용하면서 "X-"가 붙은 것이 표준이 됨

### 👉 사용 방법
* XFF에서 첫 번째 항목으로 클라이언트의 IP를 알 수 있음
* 프록시 서버의 IP는 메시지가 지나온 서버의 순서대로 나열

```
X-Forwarded-For: client, proxy1, proxy2
```
