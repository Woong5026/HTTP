### HTTP 개요

HTTP 메시지는 시작 라인, 헤더 필드, 본문으로 구성되어 있음을 앞서 배웠다. 여기서 헤더 필드에 대해 자세히 알아보자. <br/>
헤더는 HTTP 전송에 필요한 모든 부가 정보를 담고 있다. (메시지 바디의 내용, 메시지 바디의 크기 등)


과거에는 RFC2616이라는 모델로 헤더를 크게 General, Request, Response, Entity 헤더로 분류했다. <br/>
그러나 현대에 들어서 RFC7230~7235로 들어서면서 Entity를 표현(Representation)으로 명시하기 시작했다.

 ![image](https://user-images.githubusercontent.com/78454649/165423528-1ef7c635-e28e-4902-9acd-d731d96c6c32.png)


RFC7230은 메시지 본문을 통해 표현 데이터를 전달한다. 여기서 메시지 본문을 페이로드(payload)라고 부르기도 한다.

표현은 요청이나 응답에서 전달할 실제 데이터이고 표현 헤더는 이런 표현 데이터를 해석할 수 있는 정보를 제공한다. <br/>
(데이터 유형(html, json), 데이터 길이, 압축 정보 등)

<br/>

---

### 표현

<br/>

표현 데이터를 어떤 식으로 전송, 응답할 것인지 나타내기 위해 표현 헤더에서 여러 기능들을 제공한다.

![image](https://user-images.githubusercontent.com/78454649/165424063-cf48c4e1-a932-4b2d-802f-bcb65edcc0cf.png)


• Content-Type: 표현 데이터의 형식, (type이 json인지 html인지 등등) <br/>
• Content-Encoding: 표현 데이터의 압축 방식 <br/>
• Content-Language: 표현 데이터의 자연 언어 (한국어인지 영어인지) <br/>
• Content-Length: 표현 데이터의 길이 <br/>


표현 헤더는 요청, 응답 모두 사용 가능

<br/>

#### Content-type

표현 데이터의 형식 설명

ex) <br/>
Content-Type: text/html: charset=utf-8 <br/>
Content-Type: text/html: application/json

<br/>

#### Content-Encoding

표현 데이터 인코딩, 표현 데이터를 압축하기 위해 사용 <br/>
데이터를 전달하는 곳에서 압축 후 인코딩 헤더를 추가한다. 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축을 해제한다.
 
ex) <br/>
Content-Encoding: gzip <br/>
Content-Encoding: deflate

<br/>

#### Content-Language

표현 데이터의 자연 언어를 표현

ex) <br/>
Content-Language: ko <br/>
Content-Language: en

<br/>

#### Content-Length

표현 데이터의 길이, 바이트 단위 <br/>
만약 전송 코딩을 사용하면 Content-Length를 사용하면 안된다.

ex) <br/>
Content-Length: 5

<br/>

---

### 콘텐츠 협상

<br/>

클라이언트가 서버에 요청할 때 선호하는 표현 방식에 대해 협상하는 것을 콘텐츠 협상이라고 한다.

표현 헤더에 다음과 같이 나타낼 수 있다.

• Accept: 클라이언트가 선호하는 미디어 타입 전달 <br/>
• Accept-Charset: 클라이언트가 선호하는 문자 인코딩 <br/>
• Accept-Encoding: 클라이언트가 선호하는 압축 인코딩 <br/>
• Accept-Language: 클라이언트가 선호하는 자연 언어

-> 협상 헤더는 요청시에만 사용한다.

<br/>

**Accept-Language 적용 전**


![image](https://user-images.githubusercontent.com/78454649/165440690-12f3f4b3-d301-400f-a332-a56ee6ed4210.png)

만약 한국에서 요청하는 경우라면 한국어를 지원했으면 하지만 어떠한 협상도 없기 때문에 서버는 기본 언어인 영어를 제공한다.

<br/>

**Accept-Language 적용 후**


![image](https://user-images.githubusercontent.com/78454649/165440780-37f23da9-1f8f-481d-8251-9fdf43a0887f.png)

이처럼 Accept-Language: ko로 협상을 시도했을 때 서버에서 지원하는 언어라면 그에 맞는 언어를 제공한다.

<br/>

**Accept-Language 복잡한 예시**

![image](https://user-images.githubusercontent.com/78454649/165440846-bb117659-8869-46f8-9f76-55f140d44c59.png)

한국어로 요청을 했지만 서버에서 지원하지 않는 언어이기 때문에 기본 언어인 독일어로 제공한다. <br/>
그러나 아무리 한국인 입장이어도 독일어보다는 영어가 나을 수 있다.  <br/>
이와 같은 경우를 방지하기 위해 협상에는 우선순위를 제공한다.

<br/>

* 협상과 우선순위 1 - Quality Values(q)

q값을 0~1로 설정한다. **클수록 높은 우선순위**
ex) Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7 (q=1은 생략 가능)

-> 우선순위는 다음과 같다. <br/>
• 1. ko-KR;q=1 (q생략) <br/>
• 2. ko;q=0.9 <br/>
• 3. en-US;q=0.8 <br/>
• 4. en:q=0.7 <br/>

우선순위에 따라 위 예시를 다시 적용시켜 보자.


요청
* GET /event
* Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7

![image](https://user-images.githubusercontent.com/78454649/165440978-bf7dae8e-897a-4243-8c66-155af8f3d304.png)

서버에서는 마지막 우선순위(en;q=0.7)인 영어만 지원하기 때문에 최종 Content-Language는 en으로 설정된다.

<br/>

* 협상과 우선순위 2 - **구체적인 것이 우선한다**

Accept: text/*, text/plain, text/plain;format=flowed, */*

만약 위와 같은 헤더가 있다고 했을 때, 메시지 형식은 구체적인 것을 우선순위로 둔다.

-> 우선순위는 다음과 같다. <br/>
1. text/plain;format=flowed <br/>
2. text/plain <br/>
3. text/* <br/>
4. */* <br/>

<br/>

---

### 전송방식

<br/>


* 단순 전송 - Content-Length

ex)

HTTP/1.1 200 OK <br/>
Content-Type: text/html;charset=UTF-8 <br/>
Content-Length: 3423 <br/>

<html> <br/>
<body>...</body> <br/>
</html> <br/>

단순 길이 전송

<br/>

* 압축 전송 - Content-Encoding

ex)

HTTP/1.1 200 OK <br/>
Content-Type: text/html;charset=UTF-8 <br/>
Content-Encoding: **gzip** <br/>
Content-Length: 521 <br/>


lkj123kljoiasudlkjaweioluywlnfdo912u34ljko98udjkl

<br/>

* 분할 전송 - Transfer-Encoding

![image](https://user-images.githubusercontent.com/78454649/165441922-2daef8aa-fef1-477d-9a5a-9e6702bf5cf8.png)

ex) <br/>
HTTP/1.1 200 OK <br/>
Content-Type: text/plain <br/>
Transfer-Encoding: chunked (덩어리로 전송한다)

<br/>

* 범위 전송 - Range, Content-Range


ex) <br/>
1. 요청 <br/>
GET /event <br/>
Range: bytes=1001-2000 (1001에서 2000만 요청)

 

2. 응답 <br/>
HTTP/1.1 200 OK <br/>
Content-Type: text/plain <br/>
Content-Range: bytes 1001-2000 / 2000 -> 범위 / 마지막 위치 <br/>

qweqwe1l2iu3019u2oehj1987askjh3q98y

요청한 범위에 따라 메시지를 응답하는 방식이다.

<br/>

---

### 일반 정보

<br/>

앞서 공부한 것 이외에 일반적인 다른 헤더들도 알아보자.

* Referer - 이전 웹 페이지 주소

현재 요청된 페이지의 이전 웹페이지 주소를 나타낸다. A -> B로 이동하는 경우 B를 요청할 때 Referer: A를 포함해서 요청한다. <br/>
Referer를 사용함으로써 유입 경로 분석이 가능하다.

요청에서 사용한다.

<br/>

* User-Agent - 유저 에이전트 애플리케이션 정보

클라이언트의 애플리케이션 정보(웹 브라우저 정보 등)를 알 수 있다. <br/>
어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능하다. <br/>
요청에서 사용한다. <br/>

<br/>

* Server - 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보

요청이 서버까지 도달하는데 수많은 프록시, 캐시 서버를 거치게 되는데 ORIGIN 서버는 최종 종착지 서버를 말한다.

응답에서 사용한다.


<br/>

---

### 특별한 정보

<br/>

이번엔 특별한 헤더들에 대해 알아보자.

<br/>

* Host - 요청한 호스트 정보(도메인)

요청 시 사용하는 중요하고 필수적인 헤더다.

하나의 서버가 여러 도메인을 처리하는 경우가 있다. 즉, 하나의 IP 주소에 여러 도메인이 적용되어 있을 때를 생각해보자.

![image](https://user-images.githubusercontent.com/78454649/165443783-be21d86f-e975-43c4-b9cf-a1c22b067a64.png)


클라이언트와 서버는 TCP/IP를 기반으로 IP주소에 따라서 통신을 하게 되는데 이 때, <br/> 
단순히 IP주소로만 통신을 하게 된다면 어떤 도메인에 접근해야 하는지 알 수가 없다. 그래서 따로 호스트 정보를 명시해 주는 것이다.

GET /hello HTTP/1.1 <br/>
(X)

<br/>

GET /hello HTTP/1.1 <br/>
Host: aaa.com  <br/>
(O) <br/>


<br/>

* Location - 페이지 리다이렉션

이전 포스팅에서 공부한 HTTP 상태코드에서 3xx 메시지에 대해 리다이렉션을 위해 Location 헤더를 사용하는 것을 알 수 있었다.

웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동한다.<br/>
뿐만 아니라 201(Created) 코드에서 요청에 의해 생성된 리소스 URI를 나타낼 때도 Location 헤더를 사용한다.

<br/>

* Allow - 허용 가능한 HTTP 메서드

405(Method Not Allowed) 응답 코드에 포함해야 한다. <br/>
현재 서버가 어떤 HTTP 메서드를 지원하는지 알 수 있다.

ex) <br/>
Allow: GET, HEAD, PUT

<br/>

* Retry-After - 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간


503(Service Unavailable) 응답 코드에서 서비스가 언제까지 불능인지 알려줄 수 있다.

ex) <br/>
Retry-After: Fri, 31 Dec 1999 23:59:59 GMT


<br/>


---

### 쿠키

<br/>

클라이언트, 서버 통신에서 많이 사용하는 쿠키에 대해 알아보자.
* Set-Cookie : 서버에서 클라이언트로 쿠키 전달(응답)
* Cookie : 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달

쿠키는 클라이언트의 쿠키 저장소에 사용자 정보를 저장하고 있다가 요청 시 서버에 자동으로 사용자 정보를 보내는 기능을 한다. <br/>
만약 쿠키를 사용하지 않는다면 어떻게 될까?


* 간단한 요청, 응답 상황을 보자.

1. 요청 <br/>
GET /welcome HTTP/1.1

2. 응답 <br/>
HTTP/1.1 200 OK <br/>
안녕하세요. 손님

<br/> 

* 여기서 회원이 로그인을 하는 상황을 생각해보자.

1. 요청 <br/>
POST /login HTTP/1.1 <br/>
user=홍길동

2. 응답 <br/>
HTTP/1.1 200 OK <br/>
홍길동님이 로그인했습니다.

 <br/>

* 로그인 이후 다시 welcome 페이지에 접근해보자.

1. 요청 <br/>
GET /welcome HTTP/1.1

2. 응답 <br/>
HTTP/1.1 200 OK <br/>
안녕하세요. 손님 


<br/><br/>

Stateless, 로그인 기록을 유지하지 않는다.("안녕하세요. 홍길동님" 메시지를 받고 싶은데...)

HTTP는 무상태 프로토콜이다. 클라이언트와 서버가 요청과 응답을 주고 받으면 연결이 끊어지고 클라이언트가 <br/>
다시 요청하면 서버는 이전 요청을 기억하지 못한다. 즉, 서로 상태를 유지하지 않는다.

<br/>

그렇다면 모든 요청에 사용자 정보를 포함하는건 어떨까?

1. 요청 <br/>
GET /welcome?user=홍길동 HTTP/1.1 <br/>

2. 응답 <br/>
HTTP/1.1 200 OK <br/>
안녕하세요. 홍길동님 <br/>
-> 원하는 메시지를 받을 수 있다. 그러나 이는 이후 모든 요청에 사용자 정보가 포함되도록 개발해야한다. <br/>

이런 불편함을 없애는 좋은 도구가 바로 쿠키다. 위 예시에서 쿠키를 적용해보자.


![image](https://user-images.githubusercontent.com/78454649/165457756-26eb4719-04f5-46f9-b28f-c9f54c67636a.png)

* 로그인 상황

1. 요청 <br/>
POST /login HTTP/1.1 <br/>
user=홍길동
 
2. 응답 <br/>
HTTP/1.1 200 OK <br/>
Set-Cookie: user=홍길동 <br/>
홍길동님이 로그인했습니다. <br/>

-> 여기서 발생한 사용자 정보 쿠키를 클라이언트 쿠키 저장소에 저장한다.

![image](https://user-images.githubusercontent.com/78454649/165457788-0a9ccd2d-952b-477f-bce8-d28ef836195c.png)

* 로그인 이후 welcome 페이지 접근

1. 요청 <br/>
GET /welcome HTTP/1.1 <br/>
Cookie: user=홍길동


2. 응답 <br/>
HTTP/1.1 200 OK <br/>
안녕하세요. 홍길동님 <br/>

-> 클라이언트는 요청 시 자동으로 쿠키 정보를 포함한다. 따라서 무상태인 상황에서도 원하는 사용자 맞춤 메시지를 얻을 수 있다.

<br/><br/>

#### 쿠키 구조

앞서 쿠키가 쓰이는 예시를 봤는데 실제 쿠키의 구조는 다음과 같다.

set-cookie: **sessionId=abcde1234**; **expires**=Sat, 26-Dec-2020 00:00:00 GMT; **path**=/; **domain**=.google.com; **Secure**

* sessionId - 세션 ID

실제 쿠키는 직접 사용자 정보를 저장하지 않고 미리 서버의 DB 등에 저장해 놓은 세션 ID를 부여함으로써 사용자를 기억한다.

<br/>

* expires - 생명 주기

만료일이 되면 쿠키를 삭제한다.

+) Set-Cookie: max-age=3600 (3600초) : 이런 표현도 가능하다.
* 세션 쿠키 : 만료 날짜를 생략하면 브라우저 종료시까지만 유지
* 영속 쿠키 : 만료 날짜를 입력하면 해당 날짜까지 유지

<br/>


* domain - 도메인

명시한 문서 기준 도메인 + 서브 도메인 포함해서 쿠키를 생성한다. <br/>
ex) domain=example.org <br/>
여기서 example.org는 물론이고 아래 서브 도메인의 쿠키에도 접근한다.


만약 domain을 생략한다면 현재 문서 기준 도메인만 적용한다. <br/>
즉, 생략시 example.org에만 쿠키를 접근하고 서브 도메인에는 쿠키를 접근하지 않는다.

<br/>

* Path - 경로

이 경로를 포함한 하위 경로 페이지만 쿠키 접근

ex) path=/home <br/>
/home -> 가능 <br/>
/home/level1 -> 가능 <br/>
/home/level1/level2 -> 가능 <br/>
/hello -> 불가능 <br/>
+) 일반적으로 path=/루트로 지정한다.


<br/>

* 보안

Secure - 쿠키는 http, https를 구분하지 않고 전송한다. 만약 Secure을 적용하면 https인 경우에만 전송한다.

HttpOnly - 자바스크립트에서 접근 불가(document.cookie), HTTP 전송에만 사용한다.

sameSite - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우만 쿠키 전송








