# HTTP 헤더 - 일반 헤더

 ## 1. HTTP 헤더

- header-field = field-name ":" OWS field-value OWS (OWS:띄어쓰기 허용)
- field-name은 대소문자 구분 하지 않는다.
- 용도
  - HTTP 전송에 필요한 모든 부가 정보를 의미한다.
  - ex) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증...
  - 표준 헤더가 너무 많다.
  - 필요시 임의의 헤더를 추가할 수 있다.

### 1) RFC2616(현재는 폐기됨)

- 헤더 분류
  - General 헤더: 메시지 전체에 적용되는 정보 ex) Connection: close
  - Request 헤더: 요청 정보 ex) User-Agent: Mozilla/5.0 (Macintosh; ..)
  - Response 헤더: 응답 정보 ex) Server: Apache
  - Entity 헤더: 엔티티 바디 정보 ex) Content-Type: text/html, Content-Length: 3423
- HTTP body : message body
  - messgae body은 entity body을 전달하는데 사용한다.
  - entity body는 요청이나 응답에서 전달할 실제 데이터를 가리킨다.
  - entity header는 entity body의 데이터를 해석할 수 있는 정보를 제공한다.
    - ex) 데이터 유형(html, json), 데이터 길이, 압축 정보...

### 2) RFC723x

- 변화 내용
  - Entity → Representation
  - Representation = Representation Metadata + Representation Data
- HTTP body : message body
  - message  body를 통해 표현 데이터 전달
  - 메시지 본문 = payload
  - 표현은 요청이나 응답에서 전달할 실제 데이터를 의미한다.
  - 표현 헤더는 표현 데이터를 해석할 수 있는 정보를 제공한다.
    - 데이터 유형(html, json), 데이터 길이, 압축 정보...
  - 일반적으로 표현 헤더는 표현 메타 데이터와, 페이로드 메시지를 구분해야 한다.

<br>

## 2. 표현

> 표현 헤더는 전송, 응답에서 둘 다 사용 가능하다.

### 1) Content-Type

- 표현 데이터의 형식
- 미디어 타입, 문자 인코딩
- ex) `text/html; charset=utf-8`, `application/json`,  `image/png`

### 2) Content-Encoding

- 표현 데이터의 압축 방식
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더를 추가한다.
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축을 해제한다.
- ex) `gzip`: 압축, `deflate`, `identity`: 똑같음/압축하지 않음

### 3) Content-Language

- 표현 데이터의 자연 언어 표현
- ex) `ko`, `en`, `en-US`

### 4) Content-Length

- 표현 데이터의 길이
- 바이트 단위
- Transfer-Encoding(전송 코딩)을 사용하면 Content-length를 사용하면 안 된다.

<br>

## 3. 협상

> 클라이언트가 선호하는 표현을 요청한다. 협상 헤더는 **요청시에만 사용**한다.

- Accept : 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset : 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding : 클라이언트가 선호하는 압축 인코딩
- Accept-Language : 클라이언트가 선호하는 자연 언어

### 1) 협상과 우선순위 1

- `Quality Valued(q)` 값 사용
- 0~1, 클수록 높은 우선순위를 가진다.
- 생략하면 1로 인식한다.
- ex) `Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7`
  1. ko-KR;q=1 (q생략)
  2. ko;q=0.9
  3. en-US;q=0.8
  4. en:q=0.7

### 2) 협상과 우선순위 2

- 구체적인 것이 우선한다.
- ex) `Accept: text/*, text/plain, text/plain;format=flowed, */*`
  1. text/plain;format=flowed
  2. text/plain
  3. text/*
  4. \*/*

### 3) 협상과 우선순위 3

- 구체적인 것을 기준으로 미디어 타입을 맞춘다.
- ex) `Accept: text/*;q=0.3, text/html;q=0.7, text/html;level=1, text/html;level=2;q=0.4, */*;q=0.5`

<br>

## 4. 전송 방식

### 1) 단순 전송

- `Content-Length`

### 2) 압축 전송

- `Content-Encoding`

### 3) 분할 전송

- `Transfer-Encoding: chunked`
- 분할 전송 때는 Content-Lenght 를 보내면 안 된다.

### 4) 범위 전송

- 요청 : `Range`
- 응답 : `Content-Range
- ex) 이미지 절반만 받았다가 중단한 후 다시 받을 때 절반 이후부터 받는 경우

<br>

## 5. 일반 정보

### 1) From

- 유저 에이전트의 이메일 정보
- 일반적으로 잘 사용되지 않는다.
- 검색 엔진 같은 곳에서 주로 사용한다.
- 요청에서 사용한다.

### 2) Referer

- 현재 요청된 페이지의 이전 웹 페이지 주소
- 많이 사용된다.
- A → B로 이동하는 경우 B를 요청할 때 `Referer: A`를 포함해서 요청한다.
- 유입 경로 분석이 가능하다.
- 요청에서 사용한다.

### 3) User-Agent

- 클라이언트의 애플리케이션 정보 ex) 웹 브라우저 정보 ...
- 통계 정보
- 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능하다.
- 요청에서 사용한다.

### 4) Server

- 요청을 처리하는 Origin 서버의 소프트웨어 정보
- 진짜 나의 요청이 이루어지는 곳을 의미한다.
- 응답에서 사용한다.

### 5) Date

- 메시지가 발생한 날짜와 시간
- 응답에서 사용한다.
  - 과거에는 요청에서도 사용했었다.

<br>

## 6. 특별한 정보

### 1) Host ⭐

- 요청한 호스트 정보(도메인)
- 요청에서 사용한다.
- **필수요소**
  - 초창기에 문제가 많이 발생해서 필수로 지정된 것
- 하나의 서버가 여러 도메인을 처리해야하거나 하나의 IP 주소에 여러 도메인이 적용되어 있는 경우, HOST를 지정해 놓지 않으면 어떤 서버에 요청을 보내야 하는지 판단할 수 없다.

### 2) Location

- 페이지 리다이렉션
- 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동한다(리다이렉트)
- 201 (Created) : Location 값은 요청에 의해 생상된 리소스 URI
- 3xx (Redirection) : Location 값은 요청을 자동으로 리다이렉션 하기 위한 대상 리소스를 가리킨다.

### 3) Allow

- 허용 가능한 HTTP 메서드
- 405 (Method Not Allowed) 에서 응답에 포함해야 한다.
- ex) `Allow: GET, HEAD, PUT`

### 4) Retry-After

- 유저 에이저트가 다음 요청을 하기까지 기다려야 하는 시간
- 503 (Service Unavailable) : 서비스가 언제까지 불능인지 알려줄 수 있다.
- 날짜 표기 / 초단위 표기 둘 다 가능하다.

<br>

## 7. 인증

- Authorization
  - 클라이언트 인증 정보를 서버에 전달한다.
  - ex) Authorization: Basic xxxxxxxxxxxxxxxx
- WWW-Authenticate
  - 리소스 접근시 필요한 인증 방법을 정의한다.
  - 401 Unauthorized 응답과 함께 사용한다.

<br>

## 8. 쿠키

- `Set-Cookie` : 서버에서 클라이언트로 쿠키 전달(응답)
- `Cookie` : 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달한다.

### 1) 쿠키 미사용

- HTTP는 무상태(Stateless) 프로토콜이기 때문에 클라이언트와 서버가 요청과 응답을 주고 받으면 연결이 끊어진다.
- 클라이언트가 다시 요청해도 서버는 이전 요청을 기억하지 못한다. 즉, 클라이언트와 서버는 서로 상태를 유지하지 않는다.
- 그래서 로그인을 한 경우에도 페이지를 다시 요청하면, 로그인 정보를 기억하지 못한다는 문제가 발생한다.

### 2) 쿠키

- 유저가 로그인을 하면 서버에서 `Set-Cookie`에 유저 정보를 함께 담아서 응답한다.
- 클라이언트는 서버가 보낸 정보를 쿠키 저장소에 저장하고, 쿠키 저장소에서 조회하여 `Cookie` 에 조회한 데이터를 다시 전송한다.
- 주로 사용자 로그인 세션 관리, 광고 정보 트래킹 등에 사용된다.
- 쿠키 정보는 항상 서버에 전송된다.
  - 네트워크 트래픽 추가 유발
  - 최소한의 정보만 사용(세션 id, 인증 토큰)
  - 서버에 전송하지 않고, 웹 브라우저 내부에 데이터를 저장하고 싶으면 웹 스토리지(localStorage, sessionStorage)를 이용한다.
- 보안에 민감한 데이터(주민등록번호, 카드 번호..)는 저장하지 않도록 주의한다.

### 3) 생명주기

- `expires` : 만료일이 되면 쿠키를 삭제한다.
- `max-age` 0이나 음수를 지정하면 쿠키를 삭제한다.
- 세션 쿠키 : 만료 날짜를 생략하면 브라우저 종료시까지만 유지한다.
- 영속 쿠키 : 만료 날짜를 입력하면 해당 날짜까지 유지한다.

### 4) 도메인

- ex) `domain=example.org`
- 명시 : 명시한 문서 기준 도메인 + 서브 도메인을 포함한다.
  - domain=example.org를 지정해서 쿠키 생성한다.
    - example.org는 물론이고 dev.example.org에도 쿠키 접근한다.
- 생략 : 현재 문서 기준 도메인만 적용한다.
  - example.org 에서 쿠키를 생성하고 domain 지정을 생략
    - example.org 에서만 쿠키 접근하고 dev.example.org는 쿠키 미접근한다.

### 5) 경로

- 지정한 경로를 포함한 하위 경로 페이지만 쿠키 접근한다.
- 일반적으로 `path=/루트` 로 지정한다.

### 6) 보안

- `Secure` 
  - 쿠키는 http, https를 구분하지 않고 전송한다.
  - Secure를 적용하면 https인 경우에만 전송한다.
- `HttpOnly`
  - XSS 공격을 방지한다.
  - 자바스크립트에서 접근이 불가하다.
  - HTTP 전송에만 사용한다.
- `SameSite`
  - XSRF 공격을 방지한다.
  - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우에만 쿠키를 전송한다.







