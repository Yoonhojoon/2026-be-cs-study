# REST와 RESTful API

## 학습 목표

- REST가 단순한 API 명명 규칙이 아니라 아키텍처 스타일이라는 점을 설명할 수 있다.
- RESTful의 의미와, HTTP를 사용한다고 해서 자동으로 RESTful해지는 것은 아니라는 점을 설명할 수 있다.
- REST와 HTTP의 차이를 구분하고, HTTP가 REST 구현에 자주 사용되는 이유를 설명할 수 있다.
- 면접에서 REST API 관련 꼬리 질문에 답할 수 있다.

---

## 먼저 한 문장으로 정리

REST는 웹 같은 분산 시스템을 오래 유지하고 확장하기 위해 제안된 **아키텍처 스타일**이다.

RESTful은 그 REST 스타일의 제약 조건을 잘 따르는 시스템이나 API를 가리키는 표현이다.

HTTP는 REST가 아니다. HTTP는 REST의 여러 제약을 표현하기 좋아서 REST API를 만들 때 가장 널리 사용되는 **애플리케이션 계층 프로토콜**이다.

```text
REST     = 아키텍처 스타일, 설계 철학
RESTful  = REST 제약을 잘 따르는 성질
HTTP     = 클라이언트와 서버가 메시지를 주고받기 위한 프로토콜
```

---

## REST가 등장한 배경

REST는 Roy Fielding의 박사논문에서 정리된 개념이다.

Fielding은 웹이 단순한 문서 전달 시스템을 넘어, 여러 조직과 서버, 클라이언트, 프록시, 캐시가 함께 동작하는 거대한 분산 시스템이 되려면 몇 가지 제약이 필요하다고 보았다.

즉 REST는 다음과 같은 목표를 위해 등장했다.

- 서버와 클라이언트가 독립적으로 발전할 수 있게 한다.
- 서버가 많은 요청을 확장성 있게 처리할 수 있게 한다.
- 중간 계층, 캐시, 프록시가 메시지를 이해하고 활용할 수 있게 한다.
- 리소스의 내부 구현이 바뀌어도 외부 인터페이스는 안정적으로 유지되게 한다.

여기서 중요한 점은 REST가 "URL은 명사로 쓰자" 정도의 규칙이 아니라는 것이다. 그것은 REST를 HTTP 위에서 구현할 때 나타나는 실무적 관례에 가깝다.

---

## REST의 핵심 개념

### 리소스

REST에서 가장 중요한 추상화는 **리소스(Resource)** 다.

리소스는 이름을 붙일 수 있는 모든 대상이다.

예를 들어 다음은 모두 리소스가 될 수 있다.

- 회원 한 명
- 게시글 하나
- 게시글 목록
- 예약 내역
- 오늘의 날씨
- 이미지 파일

리소스는 보통 URI로 식별한다.

```http
/members/1
/articles/10
/reservations
```

여기서 `/members/1`은 "1번 회원"이라는 리소스를 식별한다.

### 표현

클라이언트와 서버는 리소스 자체를 직접 주고받지 않는다.

대신 리소스의 상태를 나타내는 **표현(Representation)** 을 주고받는다.

예를 들어 `GET /members/1` 요청에 대해 서버가 다음 JSON을 응답했다면, 이 JSON은 1번 회원 리소스의 표현이다.

```http
GET /members/1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "name": "June",
  "email": "june@example.com"
}
```

같은 리소스라도 표현 형식은 여러 가지일 수 있다.

```text
리소스: /members/1
표현: JSON, XML, HTML 등
```

그래서 REST의 이름도 Representational State Transfer, 즉 표현 상태 전이를 의미한다.

---

## REST의 제약 조건

REST는 다음 제약 조건들을 따르는 아키텍처 스타일이다.

### 1. Client-Server

클라이언트와 서버의 관심사를 분리한다.

클라이언트는 사용자 인터페이스와 사용자 경험에 집중하고, 서버는 데이터 저장과 비즈니스 로직 처리에 집중한다.

이렇게 분리하면 클라이언트와 서버가 서로 독립적으로 발전할 수 있다.

예를 들어 모바일 앱의 UI가 바뀌어도 서버의 데이터 저장 방식은 그대로 둘 수 있고, 서버의 내부 구현이 바뀌어도 API 계약이 유지되면 클라이언트는 영향을 덜 받는다.

### 2. Stateless

각 요청은 그 요청을 처리하는 데 필요한 정보를 모두 포함해야 한다.

서버는 이전 요청의 문맥을 기억하고 있다고 가정하면 안 된다.

```http
GET /my-page
Authorization: Bearer ...
```

이 요청에서 서버는 `Authorization` 헤더를 보고 사용자를 식별해야 한다. "이전에 로그인 요청을 했으니까 알겠지"라고 처리하면 REST의 무상태 제약과 멀어진다.

무상태의 장점은 확장성이다.

서버가 클라이언트별 세션 상태를 들고 있지 않으면, 요청을 여러 서버로 분산하기 쉽다.

### 3. Cache

응답은 캐시 가능 여부를 표현할 수 있어야 한다.

캐시를 잘 활용하면 같은 요청에 대해 서버까지 가지 않고도 응답을 재사용할 수 있다.

```http
HTTP/1.1 200 OK
Cache-Control: max-age=60
ETag: "abc123"
```

캐시는 네트워크 사용량을 줄이고, 응답 속도를 높이고, 서버 부하를 줄인다.

### 4. Uniform Interface

REST에서 가장 중요한 제약이다.

Uniform Interface는 모든 리소스를 일관된 방식으로 다루게 한다.

Fielding은 이를 위해 다음 네 가지 하위 제약을 설명한다.

| 하위 제약 | 의미 |
| --- | --- |
| 리소스 식별 | 리소스는 URI 같은 식별자로 구분된다. |
| 표현을 통한 리소스 조작 | 클라이언트는 표현을 주고받으며 리소스를 조회하거나 변경한다. |
| 자기 서술적 메시지 | 메시지만 보고도 처리 방법을 이해할 수 있어야 한다. 예를 들어 `Content-Type`이 필요하다. |
| HATEOAS | 다음 가능한 상태 전이가 응답 안의 하이퍼미디어로 제공되어야 한다. |

실무에서 REST를 말할 때는 보통 리소스 식별과 표준화된 인터페이스 사용까지만 이야기하는 경우가 많다. 하지만 원전 기준으로 엄밀히 말하면 HATEOAS까지 포함되어야 RESTful에 더 가깝다.

### 5. Layered System

클라이언트는 자신이 실제 서버와 직접 통신하는지, 프록시나 로드 밸런서를 거치는지 알 필요가 없다.

```text
Client -> CDN -> Load Balancer -> Server
```

이런 계층 구조는 보안, 캐싱, 로드 밸런싱, 확장성에 유리하다.

### 6. Code-On-Demand

서버가 클라이언트에게 실행 가능한 코드를 내려 클라이언트 기능을 확장할 수 있다는 제약이다.

예를 들어 브라우저가 JavaScript를 내려받아 실행하는 상황을 떠올릴 수 있다.

다만 Code-On-Demand는 REST에서 선택 제약이다.

---

## RESTful이란?

RESTful은 REST의 제약 조건을 잘 따르는 성질을 말한다.

따라서 RESTful API란 REST 스타일에 맞게 설계된 API라고 볼 수 있다.

실무에서 흔히 말하는 RESTful API는 보통 다음 특징을 가진다.

- 리소스를 URI로 표현한다.
- 행위는 프로토콜이 제공하는 표준 인터페이스로 표현한다.
- 요청과 응답은 무상태로 처리한다.
- HTTP 상태 코드를 의미에 맞게 사용한다.
- JSON, XML, HTML 같은 표현을 주고받는다.
- 캐시, 콘텐츠 협상, 인증 헤더 등 HTTP의 표준 기능을 활용한다.

예를 들어 다음은 REST 스타일에 가까운 API다.

```text
/members
/members/1
/members/1/reservations
```

반대로 다음은 HTTP를 쓰고 있지만 REST스럽다고 보기 어렵다.

```http
POST /getMember
POST /deleteMember
POST /updateMemberName
```

이 API는 리소스보다 동작을 중심으로 URI를 설계하고 있다. 그래서 REST보다는 RPC 스타일에 가깝다.

---

## HTTP와 REST의 차이

REST와 HTTP는 같은 층위의 개념이 아니다.

| 구분 | REST | HTTP |
| --- | --- | --- |
| 정체 | 아키텍처 스타일 | 애플리케이션 계층 프로토콜 |
| 목적 | 분산 하이퍼미디어 시스템의 설계 제약 제공 | 클라이언트와 서버 사이의 메시지 교환 |
| 핵심 단위 | 리소스, 표현, 상태 전이 | 요청, 응답, 헤더, 상태 코드 |
| 예시 | Stateless, Cache, Uniform Interface | 요청 방식, 상태 코드, 헤더 |
| 관계 | HTTP 위에서 구현될 수 있음 | REST 구현에 자주 사용됨 |

즉 HTTP는 REST를 지키기 위해 널리 쓰이는 도구 중 하나다.

다만 HTTP 자체가 REST는 아니다.

HTTP를 사용해도 다음처럼 설계하면 RESTful하지 않을 수 있다.

```http
POST /api
Content-Type: application/json

{
  "method": "deleteMember",
  "memberId": 1
}
```

이 경우 HTTP는 단순한 터널처럼 쓰이고, 실제 의미는 요청 본문 안의 `method`에 숨어 있다.

반대로 REST에 가까운 설계는 리소스를 중심으로 URI를 설계하고, HTTP의 표준 의미를 적극적으로 활용한다.

```text
/members/1
```

여기서 URI는 특정 동작이 아니라 "1번 회원"이라는 리소스를 가리킨다.

---

## HTTP가 REST 구현에 잘 맞는 이유

HTTP는 REST의 여러 개념과 잘 맞는다.

### URI로 리소스를 식별할 수 있다

```http
/members/1
/articles/10/comments
```

### HTTP의 표준 인터페이스를 활용할 수 있다

HTTP는 리소스에 대해 수행할 수 있는 요청의 의미를 표준화해 둔다.

REST API는 이 표준 의미를 활용해, URI에 동작 이름을 넣기보다 리소스를 중심으로 요청을 표현한다.

### HTTP 상태 코드로 처리 결과를 표현할 수 있다

| 상태 코드 | 의미 |
| --- | --- |
| 200 OK | 요청 성공 |
| 201 Created | 리소스 생성 성공 |
| 204 No Content | 성공했지만 응답 본문 없음 |
| 400 Bad Request | 잘못된 요청 |
| 401 Unauthorized | 인증 필요 |
| 403 Forbidden | 권한 없음 |
| 404 Not Found | 리소스 없음 |
| 409 Conflict | 현재 리소스 상태와 충돌 |
| 500 Internal Server Error | 서버 내부 오류 |

### HTTP 헤더로 메타데이터를 표현할 수 있다

```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer ...
Cache-Control: max-age=60
Location: /members/1
```

이런 헤더들은 메시지를 자기 서술적으로 만드는 데 도움을 준다.

---

## REST API 리소스 설계 예시

회원 리소스를 다룬다고 해보자.

| 대상 | URI | 설명 |
| --- | --- | --- |
| 회원 컬렉션 | `/members` | 여러 회원을 나타내는 리소스 |
| 회원 단건 | `/members/1` | 1번 회원을 나타내는 리소스 |
| 회원의 예약 목록 | `/members/1/reservations` | 1번 회원과 관련된 예약 컬렉션 |

새 리소스가 생성되었을 때는 응답에서 생성 결과와 리소스 위치를 표현할 수 있다.

```http
HTTP/1.1 201 Created
Location: /members/1
Content-Type: application/json

{
  "id": 1,
  "name": "June",
  "email": "june@example.com"
}
```

여기서 `201 Created`는 리소스 생성 성공을 의미하고, `Location`은 새로 생성된 리소스의 URI를 알려준다. 실제 요청 메서드별 의미는 HTTP Method 주제에서 따로 다룬다.

---

## HATEOAS는 무엇인가?

HATEOAS는 Hypermedia As The Engine Of Application State의 약자다.

쉽게 말하면, 클라이언트가 다음에 할 수 있는 행동을 서버 응답 안에서 발견할 수 있어야 한다는 뜻이다.

예를 들어 예약 정보를 조회했을 때, 취소 가능한 예약이라면 응답에 취소 링크를 포함할 수 있다.

```json
{
  "id": 10,
  "theme": "Mystery Room",
  "date": "2026-06-18",
  "status": "CONFIRMED",
  "_links": {
    "self": {
      "href": "/reservations/10"
    },
    "cancel": {
      "href": "/reservations/10/cancel"
    }
  }
}
```

이렇게 하면 클라이언트는 서버가 제공한 표현을 보고 현재 상태에서 가능한 전이를 알 수 있다.

다만 실무에서는 HATEOAS까지 엄격하게 구현하지 않는 경우가 많다. 그래서 실무에서 말하는 REST API와 Fielding이 말한 엄밀한 REST 사이에는 간극이 있다.

면접에서는 이 차이를 알고 말하는 것이 중요하다.

---

## 흔한 오해

### REST는 HTTP API다?

아니다.

REST는 아키텍처 스타일이고, HTTP는 프로토콜이다. REST는 HTTP와 잘 맞지만 같은 개념은 아니다.

### HTTP를 쓰면 RESTful API다?

아니다.

HTTP를 사용해도 URI에 동작을 넣고, 모든 요청을 POST로 처리하고, 상태 코드나 캐시 같은 HTTP 의미를 무시하면 RESTful하다고 보기 어렵다.

### REST는 CRUD다?

아니다.

CRUD는 Create, Read, Update, Delete라는 데이터 조작 관점의 분류다. REST는 리소스와 표현, 상태 전이, 무상태성, 캐시, 균일 인터페이스 같은 제약을 포함하는 더 넓은 아키텍처 스타일이다.

HTTP의 요청 방식을 CRUD와 대략 연결해 설명하는 경우가 많지만, REST는 CRUD 매핑보다 더 넓은 개념이다.

### URI에는 무조건 명사만 써야 한다?

REST 관점에서는 리소스를 식별하는 URI가 중요하다.

따라서 `/getMember`, `/deleteMember`처럼 동작을 URI에 넣기보다는 `/members/1`처럼 리소스를 표현하는 편이 REST에 가깝다.

하지만 "명사만 쓰면 RESTful"인 것은 아니다. 무상태성, 캐시, 상태 코드, 표현, HATEOAS 같은 요소도 함께 고려해야 한다.

---

## 면접 답변용 핵심 정리

REST는 분산 하이퍼미디어 시스템을 위한 아키텍처 스타일이다.

핵심은 리소스를 URI로 식별하고, 클라이언트와 서버가 리소스의 표현을 주고받으며, 무상태 요청과 균일한 인터페이스를 통해 시스템을 확장 가능하게 만드는 것이다.

RESTful은 이러한 REST 제약을 잘 따르는 성질을 말한다.

HTTP는 REST가 아니라 프로토콜이다. 다만 URI, 상태 코드, 헤더, 캐시 같은 기능이 REST의 제약을 구현하기에 잘 맞아서 REST API에서 널리 사용된다.

---

## 예상 면접 질문과 답변

### Q1. REST가 무엇인가요?

REST는 Representational State Transfer의 약자로, 분산 하이퍼미디어 시스템을 설계하기 위한 아키텍처 스타일입니다.

핵심은 리소스를 URI로 식별하고, 클라이언트와 서버가 리소스의 표현을 주고받으며 상태를 전이한다는 점입니다. 또한 Client-Server, Stateless, Cache, Uniform Interface, Layered System 같은 제약을 통해 확장성과 독립적인 진화를 얻습니다.

### Q2. RESTful이란 무엇인가요?

RESTful은 REST의 제약 조건을 잘 따르는 성질을 말합니다.

실무적으로는 리소스를 URI로 표현하고, HTTP의 표준 인터페이스와 상태 코드를 적절히 사용하며, 무상태 요청을 유지하는 API를 RESTful API라고 부르는 경우가 많습니다.

다만 엄밀하게는 HATEOAS처럼 하이퍼미디어를 통한 상태 전이까지 고려해야 RESTful에 더 가깝습니다.

### Q3. REST와 HTTP는 어떤 차이가 있나요?

REST는 아키텍처 스타일이고 HTTP는 애플리케이션 계층 프로토콜입니다.

REST는 리소스, 표현, 무상태성, 균일 인터페이스 같은 설계 제약을 말하고, HTTP는 요청과 응답을 주고받기 위한 규칙, 상태 코드, 헤더 등을 정의합니다.

HTTP는 REST를 구현하기에 적합한 기능을 많이 제공하기 때문에 REST API에서 널리 사용됩니다.

### Q4. HTTP를 사용하면 무조건 RESTful API인가요?

아닙니다.

HTTP를 사용하더라도 모든 요청을 `POST /api`로 보내고, 실제 동작을 요청 본문이나 URI의 동사로 표현한다면 REST보다는 RPC 스타일에 가깝습니다.

RESTful하려면 HTTP의 표준 의미와 상태 코드를 활용하고, 리소스 중심으로 URI를 설계하며, 무상태성과 캐시 가능성 같은 제약을 고려해야 합니다.

### Q5. Uniform Interface가 중요한 이유는 무엇인가요?

Uniform Interface는 REST를 다른 아키텍처 스타일과 구분하는 핵심 제약입니다.

모든 리소스를 일관된 방식으로 다루게 하므로 클라이언트와 서버의 결합도를 낮춥니다. HTTP에서는 URI로 리소스를 식별하고, HTTP가 제공하는 표준 요청 의미로 리소스를 다루는 방식이 균일 인터페이스의 예가 됩니다.

### Q6. Stateless가 왜 중요한가요?

Stateless는 서버가 클라이언트의 이전 요청 상태를 저장하지 않는다는 의미입니다.

각 요청이 인증 정보나 필요한 파라미터를 모두 포함하면, 서버는 요청 하나만 보고 처리할 수 있습니다. 이 덕분에 서버 확장이 쉬워지고, 로드 밸런싱이나 장애 복구도 단순해집니다.

단점은 매 요청마다 필요한 정보가 반복되어 전송될 수 있다는 점입니다.

### Q7. REST API에서 상태 코드는 왜 중요한가요?

상태 코드는 HTTP 응답의 의미를 표준화해서 클라이언트가 결과를 일관되게 해석할 수 있게 합니다.

예를 들어 생성 성공은 `201 Created`, 요청 성공 후 본문이 없으면 `204 No Content`, 리소스가 없으면 `404 Not Found`, 현재 상태와 충돌하면 `409 Conflict`를 사용할 수 있습니다.

상태 코드를 적절히 사용하면 메시지가 더 자기 서술적이 됩니다.

### Q8. HATEOAS가 무엇인가요?

HATEOAS는 Hypermedia As The Engine Of Application State의 약자입니다.

클라이언트가 다음에 수행할 수 있는 행동을 서버 응답에 포함된 링크나 폼 같은 하이퍼미디어를 통해 발견해야 한다는 제약입니다.

예를 들어 예약 조회 응답에 `cancel` 링크가 있으면 클라이언트는 현재 예약이 취소 가능한 상태임을 응답 표현을 통해 알 수 있습니다.

### Q9. REST API와 RPC API의 차이는 무엇인가요?

REST API는 리소스를 중심으로 설계하고, 표준 인터페이스를 통해 리소스의 표현을 주고받습니다.

RPC API는 원격 함수를 호출하는 관점에 가깝습니다. 예를 들어 `/getMember`, `/deleteMember`처럼 동작 이름이 API에 드러나면 RPC 스타일에 가깝습니다.

REST는 리소스와 표현, 상태 전이에 집중하고, RPC는 동작이나 함수를 호출하는 데 집중한다고 비교할 수 있습니다.

---

## 참고 자료

- [Roy Fielding - Architectural Styles and the Design of Network-based Software Architectures, Chapter 5: REST](https://roy.gbiv.com/pubs/dissertation/rest_arch_style.htm)
- [Roy Fielding - REST APIs must be hypertext-driven](https://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)
- [RFC 9110 - HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [MDN - HTTP 개요](https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/Overview)
- [MDN - HTTP 상태 코드](https://developer.mozilla.org/ko/docs/Web/HTTP/Reference/Status)
