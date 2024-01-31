# 구글을 접속할 때 일어나는 일

### 1. 웹주소([www.google.com](http://www.google.com))를 브라우저 주소창에 입력한다.

브라우저는 사용자가 주소창에 입력한 정보가 사이트 주소인지 검색어인지 확인한다.

⇒ RFC 1738에 따라 유효한 URL이 맞는지 확인한다.

<aside>
🍒 RFC?
Request for Comments
인터넷 연구 및 개발 커뮤니티에서 인터넷 표준과 관련된 문서의 시리즈
[RFC 1738] Uniform Resource Locators (URL)

</aside>

### 2. 브라우저는 캐싱된 DNS 기록을 통해 입력된 웹주소에 대응되는 ip주소가 있는지 확인한다.

<aside>
🐣 **과정**

1. 브라우저 캐시를 확인한다.
    
    브라우저는 일정기간 동안의 DNS 기록을 저장하고 있다.
    
2. 브라우저는 OS 캐시를 확인한다.
    
    브라우저 캐시에서 웹사이트 이름의 ip주소가 발견되지 않은 경우
    
    ⇒ 브라우저는 systemcall을 통해 OS가 저장하는 DNS 기록들의 캐시에 접근한다.
    
3. Router 캐시를 확인한다.
    
    컴퓨터에 DNS 기록을 찾지 못하면
    
    ⇒ 브라우저는 DNS 기록을 캐싱하는 Router와 통신하여 찾는다.
    
4. ISP 캐시를 확인한다.
    
    브라우저는 DNS 서버를 구축하고 있는 ISP에서 DNS 기록을 찾는다.
    
</aside>

<aside>
🍒 DNS
Domain Name System
브라우저가 인터넷 자원을 로드할 수 있도록 도메인 이름을 IP 주소로 변환

</aside>

<aside>
🍒 ISP
Internet Service Provider, 인터넷 서비스 제공자
인터넷에 접속하는 수단을 제공하는 주체를 가리키는 말

</aside>

<aside>
🍒 캐시 저장 이유
캐시는 네트워크 트래픽을 조절하고, 데이터 전송 시간을 줄인다.

</aside>

### 3. 요청한 URL 캐시가 없으면, ISP의 DNS 서버가 웹주소를 호스팅하고 있는 서버의 IP 주소를 찾기 위해 DNS query를 날린다.

<aside>
🐣 **과정**

1. DNS recursor가 root name server에 연락한다.
2. root name server는 .com 도메인 name server로 redirect한다.
3. .com name server는 [google.com](http://google.com) name server로 redirect한다.
4. [google.com](http://google.com) name server는 DNS 기록에서 [www.google.com](http://www.google.com)에 매칭되는 IP주소를 찾고, DNS recursor로 보낸다.
</aside>

<aside>
🍒 위 모든 요청들은 작은 데이터 패킷을 통해 보내진다.

- `패킷 = 보내는 요청의 내용 + DNS recursor 의 IP 주소` 으로 이뤄짐
- 패킷들은 원하는 DNS 기록을 가진 DNS 서버에 도달할 떄까지 클라이언트와 서버를 여러 번 오간다
- 이때 패킷들은 routing table에 기반하여 가장 빠른 경로로 움직인다.
- 도중에 패킷이 loss되면 request fail error가 발생한다
</aside>

<aside>
🍒 DNS query
여러 다른 DNS 서버들을 검색하여 특정 사이트의 IP 주소를 찾는다.
⇒ recursive search
IP 주소를 찾을 때까지 DNS 서버에서 다른 DNS 서버를 오가면서 반복적으로 검색을 진행
찾지 못하면? 에러 발생 ⇒ 검색 중단

</aside>

<aside>
🍒 DNS recursor
ISP의 DNS 서버
인터넷을 통해 다른 DNS 서버(name server)들에게 물어 도메인 이름의 올바른 IP 주소를 찾는 책임을 갖는다.

</aside>

### 4. 브라우저가 서버와 TCP connection을 한다.

브라우저는 올바른 IP 주소를 받게 되면, 서버와 connection을 빌드한다.

<aside>
🐣 **과정 - TCP/IP 3-way handshake**

1. 클라이언트와 서버간의 데이터 패킷들이 오고 가기 위해서 TCP connection이 되어야 한다.
2. TCP/IP 3-way handshake 프로세스를 통해 클라이언트와 서버간의 connection이 이뤄진다
3. 클라이언트와 서버가 `SYN` 과 `ACK` 메시지들로 3번의 프로세스를 거친 후 연결된다
    - 과정
        1. 클라이언트 머신이 `SYN` 패킷을 서버에 보내고, connection을 open해 달라고 요청한다
        2. 서버가 새로운 connection을 시작할 port가 있다면 `SYN/ACK` 패킷으로 응답한다.
        3. 클라이언트는 `SYN/ACK` 패킷을 서버로부터 받게 되면 서버에게 잘 받았다는 의미로 `ACK` 패킷을 응답한다
</aside>

<aside>
🍒 TCP

Transmission Control Protocol
연결 지향적 프로토콜
(클라이언트와 서버가 연결된 상태에서 데이터를 주고받는 프로토콜)
신뢰성 있는 데이터 전달

</aside>

### 5. 브라우저가 웹서버에 HTTP 요청을 한다.

TCP connection이 완성되면, 데이터를 전송한다.

클라이언트의 브라우저는 GET 방식으로 [www.google.com](http://www.google.com) 웹페이지를 요청한다.

### 6. 서버가 요청을 처리하고 Response를 생성한다.

서버는 웹서버를 가지고 있다.

웹서버는 브라우저로부터 Request 받고 Request Handler에게 전달하여 Request 를 읽고 Response를 생성하게 한다.

<aside>
🍒 Request Handler

- Request, Request의 헤더, 쿠키를 읽어 요청이 무엇인지 파악한다.
- 필요에 따라 서버에 정보를 업데이트 한다.
- 그리고 Response를 특정 포맷(Json, XML, HTML)으로 작성한다
</aside>

### 7. 서버가 HTTP Response를 보낸다.

첫 줄에는 현재 Response의 상태를 의미하는 status code가 나타난다.

<aside>
🍒 Status Code

- 1xx은 정보만 담긴 메세지라는 것을 의미한다
- 2xx response가 성공적이라는 것을 의미한다
- 3xx 클라이언트를 다른 URL로 리다이렉트함을 의미한다
- 4xx 클라이언트 측에서 에러가 발생했음을 의미한다
- 5xx 서버 측에서 에러가 발생했음읠 의미한다
</aside>

### 8. 브라우저가 HTML content를 보여준다.

<aside>
🐣 **과정**
1. HTML skeleton을 rendering한다.
2. HTML tag들을 체크하고, GET 방식으로 추가적 웹페이지 정적인 요소들 (이미지, css stylesheet, javascript ..)을 요청한다.
: 이 정적인 파일들은 브라우저에 의해 캐싱되어 나중에 해당 페이지 방문시 서버로부터 다시 불러오지 않도록 한다.
3. 결과적으로 www.google.com 의 모습이 보인다

</aside>
