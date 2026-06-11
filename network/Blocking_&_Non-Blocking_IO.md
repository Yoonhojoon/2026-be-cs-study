# Blocking & Non-Blocking I/O

## 학습 목표

- Blocking과 Non-Blocking이 무엇인지 이해한다.
- 제어권의 반환 시점에 따른 Blocking과 Non-Blocking의 차이를 이해한다.

---

## I/O (Input/Output)

어떠한 기기를 통해 데이터의 입출력이 이루어지는 모든 작업을 I/O라고 한다. <br>
**컴퓨터 내부(CPU, 메모리)를 벗어나 외부 장치와 데이터를 주고받는 과정**이다.

- 디스크 I/O: 하드디스크(HDD/SSD)에 파일을 읽거나 쓰는 작업
- 네트워크 I/O: 랜카드(NIC)를 통해 외부 네트워크(다른 컴퓨터)와 데이터를 주고받는 작업
  - Blocking/Non-Blocking의 차이가 가장 극명하게 드러나는 곳이 네트워크 I/O다.
  - 디스크 I/O는 느려도 예측 가능한 시간 안에 끝나지만, 네트워크는 상대방이 언제 데이터를 보낼지 전혀 예측이 불가하다.
  - 수만 명과 통신해야 하는 서버 입장에서, 네트워크 I/O를 Blocking으로 처리하면 서버가 멈춰버리기 때문에, 네트워크의 성능을 끌어올리기 위해 네트워크에서 집중적으로 다룬다.

컴퓨터 부품 간에는 어마어마한 처리 속도 차이가 존재한다.

- CPU와 메모리(RAM): 속도 매우 빠름
- 네트워크, 디스크 (I/O 장치): CPU에 비하면 매우 느림

> CPU가 네트워크에서 데이터를 받아오거나, 디스크에 파일을 쓰는 작업(I/O)을 할 때, 이 느린 속도를 어떻게 처리해야 할까?

### User Space와 Kernel Space

우리가 작성한 애플리케이션 코드(User 레벨)는 보안과 시스템 안정성 때문에, 네트워크 카드(랜카드)나 하드디스크 같은 물리적 하드웨어에 직접 접근할 권한이 없다. 

**즉, 애플리케이션은 스스로 I/O 작업을 수행할 수 없다.**

따라서 애플리케이션의 프로세스나 스레드는 I/O 작업이 필요할 때, 반드시 커널(운영체제)에게 '네트워크에서 데이터 좀 읽어와 줘'라고 부탁을 해야 한다. <br>
이를 **시스템 콜(System Call)** 이라고 한다.

1. 애플리케이션(User)이 커널(OS)에 I/O 작업을 요청한다.
2. 실제 물리적인 I/O 작업은 커널이 수행한다.
3. 작업이 완료되면 커널이 애플리케이션에 결과를 반환한다.

### 소켓 (Socket)

운영체제에게 I/O를 부탁한다고 할 때, 네트워크 I/O를 처리하기 위해, OS가 애플리케이션에 제공하는 창구(엔드포인트)가 바로 소켓(Socket)이다.

- 비유하자면, 네트워크 주소(IP, Port)가 할당된 우체통과 같다.
- 프로세스가 데이터를 보내거나 받기 위해서는 반드시 소켓을 열어서 데이터를 쓰거나(Write), 읽어야(Read) 한다.

### 제어권 반환 시점에 따른 구분

애플리케이션이 OS에게 I/O를 부탁했는데, 네트워크가 너무 느려서 데이터가 오려면 시간이 한참 걸린다고 가정해 보자.

이때, **OS가 애플리케이션에게 통제권(제어권)을 언제 돌려주느냐**에 따라 Blocking과 Non-Blocking이 명확하게 구분된다. <br>
호출된 함수가 **바로 리턴을 하는지, 하지 않는지**가 핵심이다.

- Blocking I/O: 데이터가 준비될 때까지 애플리케이션에게 _**제어권을 돌려주지 않고 대기**_ 시킨다. (함수가 바로 리턴하지 않음)
- Non-Blocking I/O: 데이터 준비 여부와 상관없이 _**즉시 제어권을 돌려주어**_ 애플리케이션이 다른 작업을 할 수 있게 한다. (함수가 즉시 에러 코드 등을 리턴함)

---

## Blocking

![Blocking_&Non-Blocking_IO_1.png](Blocking_%26Non-Blocking_IO_1.png)

- 호출된 함수가 자신의 작업을 모두 마칠 때까지 호출된 함수에게 제어권을 넘겨주지 않고, 대기하는 방식이다.
- 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면, 다른 작업이 끝날 때까지 기다렸다가 자신의 작업을 시작한다. 
- 가장 기본적인 I/O 모델로, Linux에서 모든 소켓 통신은 기본 Blocking으로 동작한다.
- I/O 작업이 진행되는 동안, 유저 프로세스는 자신의 작업을 중단한 채, 대기한다.

```
(1) Process(Thread)가 Kernel에게 I/O를 요청하는 함수를 호출
(2) Kernel이 작업을 완료하면 작업 결과를 반환 받음.

1. 유저가 커널에게 read 작업을 요청한다. (제어권을 넘겨줌)
2. 데이터가 입력될 때까지 대기한다. (제어권을 넘겨줬기 때문, 자기 작업을 제어할 수 없음)
3. 데이터가 입력되면, 유저에게 결과가 전달되어야만 유저 자신의 작업에 복귀할 수 있다. (제어권을 넘겨 받음)
```

#### BufferedReader로 이해하는 Blocking 과정
자바의 기본적인 I/O 라이브러리인 java.io 패키지는 주로 Blocking I/O 방식을 사용한다. <br>
이는 간단한 파일 입출력이나 네트워크 통신에 적합하다.

```
BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
String message = br.readLine(); // <- 여기서 Blocking 발생!
System.out.println("다음 로직 실행");
```

- 서버 스레드가 `br.readLine()` 코드를 실행한다.
- 한 줄의 데이터가 완벽하게 들어올 때까지(즉, 클라이언트가 데이터를 보내고 엔터 \n를 칠 때까지) 기다린다.
- 만약 클라이언트가 네트워크 지연으로 인해 데이터를 아주 천천히 보내고 있다면?
- 서버의 스레드는 readLine() 함수에 갇혀서, System.out.println()으로 넘어가지 못하고 대기한다.
  - **제어권을 커널(OS)에게 넘겨주고 돌려받지 못한 상태!**

#### 코드로 보는 Blocking 

클라이언트가 1명 올 때마다 전담 스레드를 1개씩 만들어야 한다.

```
// 1. 소켓을 열고 클라이언트를 기다린다.
ServerSocket serverSocket = new ServerSocket(8080);

while (true) {
    // 💡 [Blocking 발생 1] 클라이언트가 접속할 때까지 스레드가 멈춰서 대기한다.
    Socket clientSocket = serverSocket.accept(); 
    
    // 접속한 클라이언트마다 새로운 스레드를 생성하여 처리한다.
    new Thread(() -> {
        try {
            BufferedReader br = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
            
            // 💡 [Blocking 발생 2] 클라이언트가 데이터를 보낼 때까지 스레드가 멈춰서 대기한다.
            String message = br.readLine(); 
            System.out.println("수신된 메시지: " + message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }).start();
}
```

#### 장점

- 개발자가 코드를 작성하기 쉽고 직관적이다. (위에서 아래로 자연스럽게 흐르는 코드)
- 어떤 흐름에서 에러가 났는지 디버깅하기가 매우 쉽다.

#### 단점

- 애플리케이션에서 다른 작업을 수행하지 못하고 대기하게 되므로, 자원이 낭비된다. (I/O 작업이 CPU 자원을 거의 쓰지 않기 때문)

- 여러 클라이언트가 접속하는 서버를 Blocking 방식으로 구현한다면?
  - 클라이언트별로 Blocking을 감당하기 위해 무수히 많은 Thread를 생성해야 한다.
  - Thread가 많아지면 메모리 낭비가 심해지고, CPU가 스레드를 교체하는 비용(Context Switching)이 급증하여, 서버 성능이 크게 저하된다.

---

## Non-Blocking

![Blocking_&_Non-Blocking_IO_2.png](Blocking_%26_Non-Blocking_IO_2.png)

Blocking 방식의 비효율성을 극복하고자 도입된 방식이다.

I/O 작업이 진행되는 동안, 유저 프로세스의 작업을 중단시키지 않고 즉시 제어권을 돌려받는다.

```
1. 유저가 커널에게 read 작업을 요청한다.
2. 데이터가 입력됐든 안 됐든, 요청하는 그 순간, 바로 결과가 반환된다.
   - 커널이 system call을 받자마자 제어권을 즉시 애플리케이션에게 넘겨준다.
   - 이때, 읽을 데이터가 없으면, '입력 데이터가 없음(EWOULDBLOCK 등)'이라는 에러 코드를 반환한다.
3. 입력 데이터가 있을 때까지 1, 2번을 반복한다. (이 과정을 Polling(폴링)이라고 부름)
   - 2번에서 제어권을 즉시 돌려받았기 때문에, 대기하지 않고 본인의 다른 작업을 이어가거나 다시 상태를 확인할 수 있다.
4. 입력 데이터가 있으면, 유저에게 실제 데이터 결과가 전달된다.
```

#### 코드로 보는 Non-Blocking

자바 NIO(java.nio)를 활용하면 소켓을 Non-Blocking 모드로 변경할 수 있다. <br>
흐름이 이벤트 중심으로 파편화되기 때문에 코드가 훨씬 복잡해진다.

```
// 1. 채널(소켓)을 열고 Non-Blocking 모드로 설정한다.
ServerSocketChannel serverChannel = ServerSocketChannel.open();
serverChannel.configureBlocking(false); // 💡 핵심: Blocking을 하지 않겠다고 선언!
serverChannel.bind(new InetSocketAddress(8080));

while (true) {
    // 💡 [Non-Blocking] 연결된 클라이언트가 없어도 멈추지 않고 즉시 null을 반환한다.
    SocketChannel clientChannel = serverChannel.accept();

    if (clientChannel != null) {
        // 연결된 클라이언트가 있다면, 읽기 작업도 Non-Blocking으로 설정한다.
        clientChannel.configureBlocking(false);
        // ... (이후 데이터가 있는지 Polling 하거나 이벤트 루프에 등록하여 처리)
    } else {
        // 연결된 클라이언트가 없으면 스레드는 멈추지 않고 다른 유용한 작업을 할 수 있다!
        System.out.println("다른 작업 수행 중...");
    }
}
```

#### 장점

- I/O 대기 시간 동안 스레드가 멈추지 않으므로, 하나의 스레드가 여러 클라이언트의 요청을 처리할 수 있는 등 자원을 효율적으로 쓸 수 있다.


#### 단점

- 데이터가 준비되었는지 확인하기 위해 주기적으로 계속 System Call을 호출하며 물어봐야 한다. (Polling)
- 이 과정에서 반복적인 상태 확인 로직 때문에 코드가 복잡해지고, 의미 없이 CPU를 소모하는 바쁜 대기(Busy Wait) 상태가 발생하여 또 다른 자원 낭비를 초래할 수 있다.

---

## I/O 이벤트 통지 방식

Non-Blocking I/O의 반복적인 System Call 호출 문제를 해결하기 위해, I/O 이벤트 통지 방식이 도입되었다.

> 계속 물어보는 대신, 데이터가 준비되었을 때 알림(Event)을 받을 순 없을까?

#### 이벤트란?

수신 버퍼나 출력 버퍼에 데이터를 처리하는 동작을 의미한다. 

- 수신 이벤트: 입력 버퍼에 데이터가 수신되었다는 것을 알림
- 출력 이벤트: 출력 버퍼가 비었으니, 데이터 전송이 가능한 상황을 알림


#### 카카오톡 비유로 이해하기

- Non-Blocking (Polling): 상대방이 메시지를 보냈는지 확인하기 위해, 1초마다 카카오톡 앱을 껐다 켰다 하면서 채팅방을 확인해야 한다.
- 이벤트 통지 방식: 평소에는 폰을 덮어두고 다른 일을 하다가, 알림(이벤트)이 울릴 때만 앱을 열어서 메시지를 확인한다.

I/O 이벤트 통지 방식은 '작업 완료 처리를 누가 주도하느냐'에 따라 동기(Synchronous) / 비동기(Asynchronous) 모델로 다시 분류할 수 있다.

---

### 참고 자료

https://didu-story.tistory.com/307

https://bu119.tistory.com/20

https://etloveguitar.tistory.com/140
