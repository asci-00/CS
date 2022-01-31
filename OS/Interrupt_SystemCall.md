# 인터럽트와 시스템콜
### 목차
- 인터럽트
- 시스템콜

<br/><br/>

---

## 인터럽트
### 인터럽트란?
> 프로그램을 실행하는 도중에 예기치 않은 상황이 발생할 경우 현재 실행 중인 작업을 즉시 중단하고, 발생된 상황에 대한 우선 처리가 필요함을 CPU에게 알리는 것
>
> 현재 수행 중인 작업보다 중요한 일 (ex. 입출력, 우선 순위 연산 등)이 발생하면 해당 작업을 먼저 처리
>
> 종류로는 외부 인터럽트와 내부 인터럽트가 존재

#### 외부 인터럽트

> 입출력 장치, 타이밍 장치, 전원 등의 외부적인 요인으로 CPU의 하드웨어 신호에 의해 발생
> 
> ex) 전원 이상, 기계 착오, 외부 신호, 입출력
#### 내부 인터럽트

> Trap이라고 부르며, 잘못된 명령이나 데이터 사용같은 명령어의 수행에 의해 발생
> 
> 소프트웨어(사용자 프로그램)가 스스로 인터럽트 라인을 세팅
> 
> ex) 0으로 나누기가 발생, 오버플로우, 명령어를 잘못 사용한 경우 (Exception)

#### 소프트웨어 인터럽트

> 프로그램 처리 중 명령의 요청에 의해 발생한 것 (SVC 인터럽트)
> 
> ex) 프로세스 수행 중 다른 프로세스를 실행시키면 시분할 처리를 위해 자원 할당 동작이 수행

### 과정

1. 현재 수행 중인 프로세스를 멈추고, 상태 레지스터와 PC 등을 해당 프로세스의 PCB에 저장
2. *인터럽트 벡터*를 통해 ISR (Interrupt Service Routine) 주소값을 읽어 인터럽트 서비스 루틴에 진입
3. 인터럽트 작업 처리
4. 이전에 작업했던 프로세스의 레지스터 복원
5. ISR의 끝에 IRET 명령어에 의해 인터럽트가 해제
6. PC 값을 복원하여 이전 실행 위치로 복귀

> 인터럽트 벡터 : 인터럽드 발생시 처리해야 할 인터럽트 핸들러의 주소를 인터럽트 별로 보관하고 있는 테이블
> 
> Q. 인터럽트가 존재하는 이유는 무엇인가요?
> 
> A. 하드웨어에서 인터럽트 기능을 지원하지 않는다면, *폴링 방식*을 통해 우선순위가 높은 작업을 찾아 처리해야 합니다.
> 이는 인터럽트에 비해 속도와 성능 및 처리량 효율이 좋지 못함 

<pre>
📑 폴링 방식
- 사용자가 명령어를 사용해 입력 핀의 값을 계속 읽어 변화를 알아내는 방식
- 인터럽트 요청 플래그를 차례로 비교하여 우선순위가 가장 높은 인터럽트 자원을 찾아 
  이에 맞는 인터럽트 서비스 루틴을 수행
</pre>


<br/><br/>

---

## 시스템콜

### System Call이란?
> 시스템 호출을 의미하며, 
> 
> 명령어는 크게 
> 
> 메모리에서 자료를 읽어오고, CPU에서 계산을 하는 등의 `일반명령`과 
> 
> 보안이 필요한 명령으로 입출력 장치, 타이머 등의 장치를 접근하는 `특권명령` *Privileged Instruction* 이 존재하는데,
> 
> 일반명령은 모든 프로그램이 사용할 수 있으나, 특권명령은 운영체제만이 사용이 가능
>
> 운영체제는 하드웨어 보안을유지하기 위해 특권명령을 사용할 수 있는 `kernel mode`와 일반명령만을 사용할 수 있는 
> 
> `user mode`로 두개의 operation을 지원
> 
> System call 이란 user mode 로 동작하는 프로세스가 특권명령을 수행하기 위해 운영체제에 요청하는 행위를 의미

### 원리

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F25333241535CCEE810F365)

### 종류

> 프로그래밍에서 Systemcall 함수는 종류는
> 
> 큰 범주로 나눌경우 프로세스 제어, 파일 조작, 장치 조작, 정보 유지보수, 통신과 보호가 존재
> 
> 여기서는 프로세스 제어 중 자주 사용되는 몇가지를 추려 설명함

#### fork

> 새로운 자식 프로세스를 생성하는 SystamCall 함수

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    printf("pid : %d", (int) getpid()); // pid : 29146
    
    int rc = fork();
    
    if (rc < 0) exit(1); //fork 실패
    else if (rc == 0) printf("child (pid : %d)", (int) getpid());   //자식 프로세스의 경우 fork return = 0
    else printf("parent of %d (pid : %d)", rc, (int)getpid());      //부모 프로세스의 경우 fork return = child pid
        
    return 0;
}

```

> 프로세스의 수행 순서는 부모-자식 관계에 영향을 받지 않음
> 
> 부모 프로세스가 자식 프로세스보다 먼저 종료될 수 있음


#### exec

> 해당 프로세스의 이미지를 함수의 인자로 전달되는 프로그램의 이미지로 덮어씌움
> 
> 해당 명령어 or 프로그램의 code영역을 덮어 씌우고, heap, stack 등의 메모리 영역을 초기화
> 
> exec 계열은 여러 종류의 함수가 존재함

| 함수 이름	| 프로그램 지정	| 명령라인 인수	| 함수 설명 |
|---|---|---|---|
| execl	| 디렉토리와 파일 이름이 합친 전체 이름	| 인수 리스트	| 환경 설정 불가  |
| execlp |	파일 이름| 	인수 리스트| 	환경 설정 불가 |
| execle |	디렉토리와 파일 이름이 합친 전체 이름| 	인수 리스트| 	환경 설정 가능 |
| execv |	디렉토리와 파일 이름이 합친 전체 이름| 	인수 배열| 	환경 설정 불가 |
| execvp |	파일 이름	| 인수 배열| 환경 설정 불가 |
| excve |	전제 경로 명| 	인수 배열	| 환경 설정 가능 |

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
    char *command[3] = {
      "ls",   //실행할 명령어 or 프로그램 이름
      "a", //해당 프로그램에 전달될 인자
      NULL    //end 표시
    };
    execvp(command[0], command);		   // 해당 프로그램 이미지를 덮어씌움
    printf("this shouldn't print out") // 실행되지 않음.
    return 0;
}
```

#### wait

> 자식 프로세스를 생성하였을때, 부모 프로세스에서 자식프로세스의 종료를 기다림
> wait 함수의 return 값은 child의 pid 이며 인자로는 child 프로세스의 return 값을 받을 수 있는 변수의 주소값을 넣어줌

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    printf("pid : %d", (int) getpid()); // pid : 29146
    
    int rc = fork();
    
    if (rc < 0) exit(1); //fork 실패
    else if (rc == 0) printf("child (pid : %d)", (int) getpid());   //자식 프로세스의 경우 fork return = 0
    else {
      int cpid = wait(NULL);  //여기서 자식 프로세스가 종료될때까지 block 됨 
      printf("parent of %d / %d (pid : %d)", cpid, rc, (int)getpid());
    }
        
    return 0;
}

```

### 출처

#### 이미지
- https://luckyyowu.tistory.com/133
