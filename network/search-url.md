# 🔗 웹 브라우저 주소 창에 주소를 입력하고 화면이 뜰 때까지의 과정

<br>

### 👉 웹 브라우저는 URL을 분석(파싱)한다.
* 사용하는 프로토콜의 종류, 도메인 이름, 포트 등을 파악한다.
* `https://www.naver.com/`에서 프로토콜은 `https`, 도메인은 이름은 `www.naver.com`, 포트는 `443`이다. (포트 번호는 생략가능하다.)

<br>

### 👉 HSTS 목록 조회
* HSTS(HTTP Strict Transport Security)는 웹 브라우저가 HTTPS 프로토콜만 사용하여 서버와 통신하도록 하는 것이다.
* 웹 브라우저는 HSTS 목록을 조회하여 입력된 주소가 목록에 있다면 HTTPS 요청을 전송하고, 없다면 첫 요청은 HTTP로 전송한다.

<br>

### 👉 웹 브라우저는 DHCP 서버에서 사용자 PC의 IP 주소, 가장 가까운 라우터의 IP 주소, 가장 가까운 DNS 서버의 IP 주소를 받는다.
* 처음 엔터를 친 순간의 사용자 PC는 IP 주소를 갖지 않고, IP 주소를 받기 위해 DHCP 요청 메시지를 생성한다.
* DHCP 요청 메시지는 `UDP 세그먼트` -> `IP 데이터 그램` -> `Ethernet 프레임` 으로 생성된다.
  * 소스 IP: 0.0.0.0, 목적 IP: 255.255.255.255
  * 소스 MAC: 클라이언트의 IP, 목적 MAC: FF:FF:FF:FF:FF:FF
* 요청은 모든 Switch로 `Broadcasting` 된다.
  * MAC 주소를 통해 스위치와 연결된 모든 장치로 요청이 전송되고, 스위치는 모든 포트로 요청을 전송한다.
* Ethernet 프레임은 DHCP 서버에 도착하고 DHCP 요청으로 역다중화된다.
* DHCP 서버는 클라이언트의 IP 주소, 가장 가까운 라우터의 IP 주소, 가장 가까운 DNS 서버의 IP 주소를 포함한 `DHCP ACK`을 만들어 응답한다.

<br>

### 👉 웹 브라우저는 ARP를 이용해 IP 주소를 기반으로 가장 가까운 라우터의 MAC 주소를 받는다.
* DNS 쿼리를 보내기 위해서는 가장 가까운 라우터의 MAC 주소를 알아야 한다. (DNS 서버가 다른 Subnet에 존재)
* 사용자 PC는 ARP 요청 Frame을 만들어 `Broadcasting` 한다.
  * 목적 IP: 가장 가까운 라우터의 IP
  * 목적 MAC: FF:FF:FF:FF:FF:FF
* 라우터는 자신의 `MAC 주소`를 사용자 PC로 응답한다.
  * 모든 adapter가 요청을 받아 ARP 모듈로 전달해 자신의 IP와 요청의 IP를 비교한다.
  * IP가 동일하다면 응답한다.

<br>

### 👉 웹 브라우저는 가장 가까운 라우터의 MAC 주소와 IP 주소를 사용해 DNS 서버로 쿼리를 전송하고 URL의 IP 주소 를 응답받는다.
*  사용자 PC는 주소(https://www.naver.com/)가 담긴 `DNS 쿼리`를 만든다.
*  DNS 쿼리는 `UDP 세그먼트`로 생성된다. 
  * 소스 IP: 클라이언트 IP, 목적 IP : DHCP 서버로부터 응답받은 가장 가까운 DNS 서버의 IP
* DNS 서버는 DNS 쿼리를 받고, 요청된 도메인 네임에 해당하는 IP 주소를 찾아 클라이언트에게 응답한다.
* 추가: `CDN`을 사용하면 사용자에 따라 IP가 달라짐

<br>

### 👉 👉 웹 브라우저는 TCP Socket을 통해 웹 서버와 3-Way Handshaking을 하여 연결한다.
* 클라이언트는 `TCP Socket`을 만들어 `HTTP GET` 메시지를 전송한다.
* HTTP 통신은 TCP 통신으로 진행된다. TCP 통신을 위해서는 3`-Way Handshaking` 과정을 진행해야 한다.
* 클라이언트가 서버에게 `SYN` 메시지를 보낸다.
  * 목적 IP: 목적지 IP 주소
  * 목적 MAC: 가장 가까운 라우터의 MAC 주소
* 서버는 클라이언트에서 `SYNACK` 메시지를 전송한다.
* 클라이언트는 서버에게 `ACK`을 전송한다.

<br>

### 👉 HTTPS 통신을 위해 TLS(Transport Layer Security) Handshaking을 진행한다.
* 비대칭키 암호화 방식 사용
* 과정
  * 클라이언트가 자신의 버전, 암호 알고리즘 목록 등을 "Client Hello" 메시지에 넣어 서버로 전송한다.
  * 서버는 자신의 암호 알고리즘, 압축 방식, 세션 ID, 인증기관의 인증서를 "Server Hello" 메시지에 넣어 응답한다. 이 인증서에는 공개키가 들어있다.
  * 클라이언트는 자신이 가지고 있던 인증기관의 리스트를 확인하고 해당 인증기관의 공개키로 서버에게 받은 인증서를 복호화하여 서버의 공개키를 얻는다.
  * 클라이언트는 대칭키를 생성하고, 이를 서버의 공개키로 암호화하여 서버에 전송한다.
  * 서버는 자신의 개인키로 클라이언트가 보내온 대칭키를 복호화 하여 얻는다.
  * 위에서 얻은 대칭키를 이용해 데이터를 전송한다.

<br>

### 👉 HTTP 요청이 TCP Socket을 통해 보내지고, 응답으로 웹페이지의 정보가 웹 브라우저로 전달된다.
* `HTTP GET` 요청을 전송한다.

<br>

## ⛄ 용어 정리

### 👉 DHCP(Dynamic Host Configuration Protocol)
* 호스트(서버)의 IP 주소 등을 자동으로 할당하고 분배하며 IP를 유동적으로 관리하는 프로토콜
* IPv4의 IP 갯수가 제한되어 있어 발생하는 IP 관리 문제와 IP의 낭비 문제를 해결하기 위해 고안된 프로토콜
* DHCP 서버는 일정 대역의 IP를 소유하고 있어 특정 장치가 IP를 필요로 하는 경우, 해당 장치에 IP를 발급하고 발급한 IP가 일정 시간(DHCP Lease Time)
동안 사용되지 않으면 해당 IP를 회수
* 예시) 인터넷 공유기: DHCP 서버를 포함하고 있어 인터넷을 사용할 수 있음

### 👉 ARP(Address Resolution Protocol)
* IP 주소(네트워크 계층 주소)를 MAC 주소(데이터 링크 계층 주소)로 변환하는 프로토콜
* IP 주소와 MAC 주소를 매핑한 ARP Table이 존재
* `같은 Subnet에서만 동작함` (DNS는 위치 상관없이 동작)

### 👉 DNS(Domain Name System)
* Host Name을 IP 주소로 변환하는 프로토콜
* 분산 데이터베이스로 구축되며 애플리케이션 계층의 프로토콜
* 요청 순서: Local DNS 서버 → Root DNS 서버 → TLD DNS 서버 → Authoritative DNS 서버 순서로 요청하여 IP 주소를 얻음
* 서버의 구조에 따라 Iterative Query와 Recursive Query로 구별 가능

### 👉 HTTP와 HTTPS
#### HTTP(HyperText Transfer Protocol)
* HTTP는 웹 브라우저와 웹 서버 사이에서 문서를 전송하기 위한 통신 프로토콜
* 단순한 텍스트로 통신하여 데이터 유출 등 보안 문제 발생 가능
#### HTTPS(HTTP Secure Socket)
* HTTP의 보안 문제를 해결하기 위해 사용하는 프로토콜
* 통신 내용을 암호화: 비대칭키 암호화 방식(하나의 키로는 암호화, 다른 키로는 복호화)

### 👉 TCP(Transmission Control Protocol)
* 연결 지향형 통신 프로토콜
* 신뢰성 있는 통신(데이터 재전송 지원), 흐름제어와 혼잡제어 지원
* 완벽한 전송에 초점
* 예시: HTTP

### 👉 UDP(User Datagram Protocol)
* 비연결 지향형 통신 프로토콜
* 신뢰성을 보장하지 않음(데이터 재전송 미지원)
* 속도가 빠름
* 예시: DHCP, DNS

### 👉 URI와 URL
#### URI(Uniform Resource Identifier)
* 통합 자원 식별자: 자원을 유일하게 식별하는 주소
* URL, URN 두 가지의 형태 존재
* 구조

```
<protocol_name>://<host>:<port>/<path>?<query>#<fragment>
```

#### URL(Uniform Resource Location)
* 자원의 구체적인 위치
* 웹 사이트의 주소와 네트워크 상의 자원 모두를 나타낼 수 있음
* 구조

```
<protocol_name>://<host>:<port>/<path>?<query>
```

### 👉 대칭키 암호화
암호화와 복호화에 사용되는 키가 동일한 방법
데이터 전송 과정에서 키가 노출되는 보안 문제 발생 가능

### 👉 비대칭키(공개키) 암호화
암호화와 복호화에 각각 2개의 키를 사용, 대칭키 암호화 방식의 보안 문제 해결을 위해 사용
공개키: 타인에게 제공 / 개인키: 자신만이 소유

<br>

## ⛄ 네트워크 통신을 할 때, DNS로 수신처의 IP주소를 알고 ARP를 통해 수신처의 MAC주소를 알아냄. IP와 MAC주소가 모두 필요한 이유?
* 네트워크 통신에서 목적 장치의 네트워크를 찾기 위해 IP 주소를 사용하고, 해당 네트워크에서 실제 목적지를 찾기 위해 MAC 주소를 사용한다.
* 네트워크 통신에서 3계층과 2계층 사이의 정보 전달 과정이 포함되는데, 이때, IP 주소와 MAC 주소가 모두 필요하다.
* IP주소: 네트워크 계층의 주소, 변할 수 있는 주소
* MAC 주소: 장치에 부여된 고유한 식별번호, 변하지 않는 주소
