# 8기 BE CS 스터디 '지능향상스터디'

# 🌳 Rule

### 학습 방식

- 매주 공통 대주제를 선정한다.
- 각자 대주제 내에서 소주제를 선택하여 학습한다.
- 학습 내용은 Markdown 파일로 정리한다.
- 발표 2시간 전까지 PR을 생성한다.

### 발표 및 토론

- 발표는 매주 목요일 16시 ~ 18시에 진행한다.
- 각자 질문을 준비한다.
- 스터디 시간에는 발표 또는 토론 방식으로 진행한다. (추후 확정)
- 학습한 내용은 다른 사람에게 가르치듯 설명하며 공유한다.
- 당일 주제뿐만 아니라 이전 주차의 질문도 포함하여 복습 효과를 높인다.

### 참고 자료

- [JaeYeopHan/Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner)
- [gyoogle/tech-interview-for-developer](https://github.com/gyoogle/tech-interview-for-developer)
- [WeareSoft/tech-interview](https://github.com/WeareSoft/tech-interview)
- [jobhope/TechnicalNote](https://github.com/jobhope/TechnicalNote)

### 참여 규칙

- 불참 2회 시 두바이 아이스박스 사기

# 👨‍💻 스터디원

| 이름  | GitHub                                       |
|-----|----------------------------------------------|
| 봉구스 | [@toctoce](https://github.com/toctoce)       |
| 라이  | [@dhyepark](https://github.com/dhyepark)     |
| 티모  | [@yoonhojoon](https://github.com/yoonhojoon) |
| 서여  | [@yeo-li](https://github.com/yeo-li)         |

# 학습 주제

학습주제는 [gyoogle](https://gyoogle.dev/)을 참고했습니다.

## 📌 네트워크

- OSI 7 계층
- TCP 3 way handshake & 4 way handshake
- TCP/IP 흐름제어 & 혼잡제어
    - [TCP/IP 흐름 제어](network/TCP_흐름_제어.md)
    - TCP/IP 혼잡 제어
- TCPvsUDP
- 대칭키 & 공개키
- HTTP & HTTPS
- [로드 밸런싱(Load Balancing)](network/로드_밸런싱(Load_Balancing).md)
    - [로드 밸런싱의 이점](network/로드_밸런싱의_이점.md)
    - [로드 밸런싱의 유형](network/로드_밸런싱의_유형.md)
    - [L4 로드 밸런싱](network/L4_로드_밸런싱.md)
    - [L7 로드 밸런싱](network/L7_로드_밸런싱.md)
- Blocking & Non-Blocking I/O

## 📌 운영체제

- 운영체제란?
- 프로세스 vs 스레드
- 프로세스 주소 공간
- 인터럽트(Interrupt)
- 시스템 콜(System Call)
- PCB와 Context Switching
- IPC(Inter Process Communication)
- CPU 스케줄링
- 데드락(DeadLock)
- Race Condition
- 세마포어(Semaphore) & 뮤텍스(Mutex)
- 페이징 & 세그먼테이션
- 페이지 교체 알고리즘
- 메모리(Memory)
- 파일 시스템

## 📌 데이터베이스

- 키(Key) 정리
- SQL - JOIN
- SQL Injection
- SQL vs NoSQL
- 이상(Anomaly)
- 정규화
- 인덱스(INDEX)
- 트랜잭션(Transaction)
- 트랜잭션 격리 수준(Transaction Isolation Level)
- 레디스(Redis)

## 📌 언어

- Java
    - Java 컴파일 과정
    - 자바 가상 머신(Java Virtual Machine)
    - Garbage Collection
    - Annotation
    - Call by Value vs Call by Reference
    - Primitive type vs Reference type
    - String & StringBuffer & StringBuilder
    - Overriding vs Overloading
    - Thread 활용
    - Casting(업캐스팅 & 다운캐스팅)
    - Promotion & Casting
    - 고유 락(Intrinsic Lock)
    - Error & Exception
    - java 8 & java 11 차이
    - Access Modifier
    - Wrapper class
- Javascript
    - JS Event Loop
    - Hoisting
    - JS Scope
    - Closure
    - this
    - Promise
    - ECMAScript6(=ES6)

## 📌 웹

- HTTP Method
- RESTFul API 란?
- 브라우저의 작동 원리
- DOM(Document Object Model)
    - Event Bubbling and Capturing
    - Event delegation
- CSS Selector 우선순위
- Reflow&Repaint
- CORS
- 크로스 브라우징
- 웹 성능 최적화
- 서버 사이드 렌더링 vs 클라이언트 사이드 렌더링
- CSS Methodology
- Normalize.css vs Reset.css
- 웹 컴포넌트
- 쿠키(Cookie) & 세션(Session)
- 웹 서버와 WAS의 차이점
- OAuth
- JWT(JSON Web Token)
- Authentication & Authorization
- 로그 레벨
- UI와 UX
- Vue.js
- React
- Vue.js vs React.js
- 네이티브 앱 & 웹 앱 & 하이브리드 앱
- PWA(Progressive Web App)

---

## 📌 알고리즘

- 거품 정렬(Bubble Sort)
- 선택 정렬(Selection Sort)
- 삽입 정렬(Insertion Sort)
- 퀵 정렬(Quick Sort)
- 합병 정렬(Merge Sort)
- 힙 정렬(Heap Sort)
- 기수 정렬(Radix Sort)
- 계수 정렬(Count Sort)
- 비트마스크(BitMask)
- 이분 탐색(Binary Search)
- 세그먼트 트리
- 해시(Hash)
- DFS & BFS
- 최장 증가 수열(LIS)
- 최소 공통 조상(LCA)
- 동적 계획법(Dynamic Programming)

## 📌 자료구조

- Array & ArrayList & LinkedList
- 스택(Stack) & 큐(Queue)
- 힙(Heap)
- 이진탐색트리(Binary Search Tree)
- 해시(Hash)
- 트라이(Trie)
- B-Tree & B+Tree

## 📌 개발상식

- 클린코드 & 리팩토링 & 시큐어코딩
- 애자일(Agile) 정리
- TDD(Test Driven Development)
- 객체 지향 프로그래밍
- 함수형 프로그래밍Ⅰ
- 함수형 프로그래밍Ⅱ
- 데브옵스(DevOps)
- 서드 파티(3rd party)란?
- Git 과 GitHub 에 대해서
- 정규식
- Generic
- final
