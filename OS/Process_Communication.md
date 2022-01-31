# IPC *InterProcess Communication*

> 프로세스의 통신은 아래와 같이 구분 가능
>
>   - 내부 통신 : Process 내 Thread 사이 통신
>   - 외부 통신 : Process 간 통신 (IPC를 통해 이루어짐)
>   - 네트워크를 통한 통신 : 통신 프로토콜에 의해 이루어짐
>       (IPC의 기술 중 네트워크를 지원하는 기술도 존재)

### 목차
- IPC
- 분류
- 설비

<br/><br/>
---

## IPC

> 일반적으로 프로세스는 다른 프로세스에게 영향을 받지 않는 독립적인 주소공간을 가짐<br/>
> 다른 프로세스와의 통신 (자료 교환)이 필요한 경우 IPC 기술을 사용해야함<br/>
> IPC설비에는 여러 종류가 존재하기 때문에 상황에 맞는 설비 사용이 필요<br/>
> **커널이 제공**하는 프로세스간 통신 기능 사용

<br/><br/>
---

## 분류


### 내부통신

> 프로세스 내부 Thread 끼리 통신하는 방식으로,
>
> Thread는 Stack영역만 고유하게 가지므로 Data영역에 속하는 전역변수를 사용하여 통신할 수 있음

### 외부 통신

> 프로세스 외부 즉, 프로세스간 통신을 하는 방식
>
> 커널에서 제공하는 IPC 설비를 사용하여 통신
>
> Message Passing 방식과 Shared Memory 방식이 존재

### #Message Passing

> 커널이 메모리 보호를 위해 대신 전달해주는것을 의미
>
> 안전하고 동기화 문제가 없음 But, 성능이 다소 떨어짐
>
> Direct Communication과 Indirect Communication 방식이 존재함

### `Direct Communication`

> 커널에 메시지를 전송하고 커널이 B에게 메시지를 전달하는 방식
>
> A -> B 형식의 통신이기 때문에, A와 B사이 연결이 존재해야 하며, 통신당 연결이 하나만 존재할 수 있음
>
> 일반적으로 양방향 통신으로 이루어짐

### `Indirect Communication`

> 커널에 미시지를 쓰면 *Write* 커널은 메시지를 보관
>
> 다른 프로세스가 커널에서 메시지를 읽어옴 *Read*
>
> 커널의 자료구조를 통해 간접적으로 통신하는 방식으로 여러 프로세스간 통신이 가능

### #Shared Memory

> 서로 다른 프로세스가 읽고 쓸 수 있는 메모리를 제공해주는 방식
>
> 커널은 메모리만 제공해주므로 Message Passing에 비해 성능이 좋음
>
> But, 커널의 동기화기능을 사용하지 않으므로, 추가적으로 동기화 부분을 기술해야함


<br/><br/>
---

## 설비

### PIPE

   > 파이프는 두 개의 프로세스를 연결하는데 하나의 프로세스는 데이터를 쓰기만 하고, 다른 하나는 데이터를 읽기만 할 수 있다.
   >
   > **한쪽 방향으로만 통신이 가능한 반이중 통신**이라고도 부른다.
   >
   > 따라서 양쪽으로 모두 송/수신을 하고 싶으면 2개의 파이프를 만들어야 함

##### 익명 PIPE - Unnamed(Anonymous) Pipe

   > 이름을 가지지 않는 pipe로 식별자가 필요하지 않아도 통신이 가능한
   >
   > 부모 - 자식 관계의 프로세스에서 사용됨

##### 이름있는 PIPE - Named Pipe
   > Named 파이프는 전혀 모르는 상태의 프로세스들 사이 통신에 사용한다.
   >
   > 즉, 익명 파이프의 확장된 상태로 **부모 프로세스와 무관한 다른 프로세스도 통신이 가능한 것** (통신을 위해 이름있는 파일을 사용)
   >
   > 네트워크 파이프를 통한 **네트워크 통신이 가능**

### Message Queue

> 입출력 방식은 Named 파이프와 동일
>
> **But**, 다른점은 메시지 큐는 파이프처럼 데이터의 흐름이 아닌 커널에 존재하는 자료구조 - Linked List -
>
> **양방향 통신이 가능**
>
> 사용할 데이터에 번호를 붙이면서 여러 프로세스가 동시에 데이터 사용 가능

### 공유 메모리 Shared Memory

   > 파이프, 메시지 큐가 통신을 이용한 설비라면, **공유 메모리는 데이터 자체를 공유하도록 지원하는 설비**다.
   >
   > 프로세스의 메모리 영역은 독립적으로 가지며 다른 프로세스가 접근하지 못하도록 설계되어있지만, 공유 메모리를 사용하면 스레드처럼 메모리를 공유하도록 커널에서 제어
   >
   > 공유 메모리는 **프로세스간 메모리 영역을 공유해서 사용할 수 있도록 허용**해준다.
   >
   > 프로세스가 공유 메모리 할당을 커널에 요청하면, 커널은 해당 프로세스에 메모리 공간을 할당해주고 이후 모든 프로세스는 해당 메모리 영역에 접근할 수 있게 된다.
   >
   > - **중개자 없이 곧바로 메모리에 접근할 수 있어서 IPC 중에 가장 빠르게 작동**

### 메모리 맵

   > 공유 메모리처럼 메모리를 공유해준다. 메모리 맵은 **열린 파일을 메모리에 맵핑시켜서 공유**하는 방식이다. (즉 공유 매개체가 파일+메모리)
   >
   > 주로 파일로 대용량 데이터를 공유해야 할 때 사용한다.

### 소켓

   > 네트워크 소켓 통신을 통해 데이터를 공유한다.
   >
   > 클라이언트와 서버가 소켓을 통해서 통신하는 구조로, 원격에서 프로세스 간 데이터를 공유할 때 사용한다.
   >
   > 서버(bind, listen, accept), 클라이언트(connect)

### RPC

![](https://media.vlpt.us/images/jakeseo_me/post/16327fcc-4da1-4a4b-8dbc-b5b84a933900/image.png)

   > 프로세스 외부에 존재하는 함수나 프로시저를 실행할 수 있게 해주는 통신 방식
   >
   > TCP/IP 를 통해 동작됨

```
* 프로시저와 함수
함수 - 인풋에 대한 아웃풋이 발생
프로시저 - 명령 단위가 수행되는 절차
```