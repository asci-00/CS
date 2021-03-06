# 메모리 관리
## 목차
- 메모리 관리 목적
- 단편화
- 메모리 할당
- 가상 메모리
- 스레싱

<br/><br/>
---

## 메모리 관리 목적


### 메모리 관리란?

> 실행하려는 프로세스는 메모리에 적재되어 있어야 함
>
> 여러개의 프로세스를 동시에 수행하는 환경에서 한정된 메모리 자원을 효율적으로 관리하기 위한 전략
>
> 메모리 관리를 위해 메모리 관리 장치 MMU *Memory Management Unit*와 OS의 메모리 관리정책이  존재

### 정책

#### 적재 정책 *Fetch Policy*
> 디스크에서 메모리로 프로세스를 언제 가져와야 할지 결정
>   - 요구 적재 *Demand Fetch* - 필요할 때 프로세스 (or 페이지 / 세그먼트) 적재
>       - overhead 최소화 / 대기시간 증가
>   - 예상 적재 *Anticipatory Fetch* - 시스템의 요청을 미리 예측하여 프로세스 적재
>       - overhead 초래 / 효율성 향상

#### 배치 정책 *Placement Policy*
> 디스크에서 메모리로 가져온 프로세스를 어느 위치에 배치할것인지 결정
>   - 최초 적합 *First Fit* - 적재 가능한 공간 중 첫번째 공간에 배치
>   - 최적 적합 *Best Fit* - 적재 가능한 공간 중 프로세스 (or 페이지 / 세그먼트) 크기에 가장 적합한 공간에 배치
>   - 최악 적합 *Worst Fit* - 적재 가능한 공간 중 가장 큰 공간에 배치
> 최적 적합이 항상 효율적이지는 않음

#### 교체 정책 *Replacement Policy*
> 메모리가 충분하지 않을 때, 현재 적재된 프로세스 중 제거할 프로세스를 결정
> LRU / LFU / FIFO 등의 교체 알고리즘이 존재

<br/><br/>
---

## 단편화

> 프로세스 (or 페이지 / 세그먼트)를 메모리에 할당 해제를 반복하다보면
>
> 메모리에 충분한 공간이 존재함에도 불구하고 프로세스를 적재시킬 수 없는 상황이 발생
>
> 이를 단편화라고 하며 외부 단편화, 내부 단편화가 존재

![](https://kouzie.github.io/assets/OS/OS_10_8.png)

*출처 : https://jaeyeon93.github.io/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%EA%B8%B0%EC%B4%88(12)%EB%A9%94%EB%AA%A8%EB%A6%AC%ED%95%A0%EB%8B%B9/*

### 내부 단편화

> 메모리를 고정 크기로 분할할 때, 발생됨
1. 고정된 크기에 프로세스(or 부분) 할당
2. 프로세스가 고정된 크기보다 작을 경우, 남는 메모리 발생
3. 2번의 과정 반복으로 잔여 메모리가 충분한데도 프로세스가 적재 못하는 상황 발생

### 외부 단편화

> 메모리를 가변 크기로 분할할 때 발생됨

1. 위와같이 메모리 할당 & 해제가 반복해서 발생 시, 흩어진 공백 *scattered holes*이 발생
2. 흩어진 잔여 공간을 합치면 충분히 프로세스를 적재할 수 있음에도 불구하고 적재가 불가능한 상태

#### 해결
1. 메모리 할당 정책으로 연속 메모리 할당 방식 사용 (최초, 최적, 최악 적합)
   1. 효율이 좋으나, 완전히 해결되지 않아 외부 단편화로 인해 1/3정도의 낭비가 발생
2. 압축 *Compaction* 방식 사용
   1. 메모리를 욺겨 흩어진 공간을 한곳으로 모으는 방식
   2. 바인딩(메모리 계산)에 의해 비용 부담이 큼

<br/><br/>
---

## 메모리 할당
> 프로세스에게 메모리를 할당할 때, 스와핑, 연속할당 과 불연속 할당으로 크게 세 가지 기법이 존재

### 스와핑 **Swapping**

> 부족한 메모리 공간을 좀 더 효율적으로 관리하려는 메모리 관리 기법.
>
> CPU에서 시행되지 않는 프로세스 즉 ready상태이거나 waiting상태에 있는 프로세스들 중 일부를
>
> 메모리 안에 보관하지 않고 하드디스크 같은 저장장치에 보관 Swap out

> #### Swap-in
> - 준비 ready 혹은 대기 waiting 상태인 프로세스를 보조기억장치에 저장 (Ready Queue로 들어가지 않음)
>   - 프로세스가 가지는 상태(리소스, 데이터, 힙, 스택 등)을 그대로 저장
> #### Swap-out
> - CPU 스케줄러에 의해 running 상태로 전환되면 보조기억장치에 저장된 데이터를 그대로 메모리에 적재
> - 메모리상에 적재되기 위해 바인딩 작업 필요

#### 장점
- 보조기억장치를 활용하여 큰 메모리를 운용하는 효율적인 메모리 관리 기법
#### 단점
- 문맥교환이 매우 비효율적
  - 보조기억장치의 속도와 메모리의 속도차이가 매우 큼

### 연속 메모리 할당
> 메모리에 프로세스를 적재할 때, 연속적인 공간에 적재시키는 것 (프로세스 자체를 분할하지 않음)
>
> 고정 분할 방법과 가변 분할 방법이 존재
>
> 단편화 문제가 심각하여 현재 사용되지 않으며, 효율이 좋은 불연속 메모리 할당이 개발됨
>
> MMU는 각 프로세스의 시작 위치만을 relocation register에 저장

#### 1. 고정 분할

- 비어있는 공간의 크기가 모두 같기 때문에, 비어있는 아무 공간에 프로세스 적재
- 메모리를 고정된 크기로 나누어 프로세스 할당
- **내부 단편화** 발생

#### 2. 가변 분할

- 고정된 경계값을 없애고 각 프로세스가 필요한 만큼 메모리를 할당하는 방법
- 비어있는 공간의 크기가 다르므로 프로세스를 할당할 때, 최초, 최적, 최악 할당기법을 사용
- 프로세스가 할당 해제를 반복하다보면 **외부 단편화** 발생

<br/><br/>
---

## 불연속 메모리 할당

> 프로세스가 연속적으로 할당되지 않고, 일정한 크기로 분할하여 메모리에 불연속적으로 적재
>
> 프로세스의 조각들이 불연속적으로 할당되므로, 해당 조각을 찾기 위한 테이블이 존재해야함
>
> 크기의 고정 여부에 따라 **페이징 기법**과 **세그멘테이션 기법**으로 구분됨
>
> 페이징 기법과 세그멘테이션 기법을 합친 **Paged Segmentation** 기법도 존재
>
> 실제 메모리는 연속적이지 않지만, MMU에 의해 연속적으로 사용하고 있다는 것을 보장

## 1. 페이징
> 프로세스의 주소공간을 동일한 크기로 분할
>
> 외부 단편화를 최소화하기 위해 사용
>
> 논리주소는 페이지 번호 p 와 오프셋 d 로 이루어짐

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FBNK7R%2FbtqAJgOOMXP%2FKXhaDdXTrz8zcAbKBjm2o1%2Fimg.png)



### 특징
- 프로세스를 **페이지** 단위로, 메모리를 **프레임** 단위로 고정된 크기로 분할
  - 페이지의 크기와 프레임의 크기는 동일
  - 프로세스는 페이지의 집합 / 메모리는 프레임의 집합
- 실제로 메모리에 적재되는 페이지는 불연속적이므로 해당 메모리를 추적하기 위한 방법이 필요
  - 각 프로세스의 페이지를 매핑해주는 PMT **Page Mapping Table**이 존재함
  - PMT는 프로세스와 함께 메인 메모리에 적재됨
  - 해당 프로세스는 PMT의 주소(PTBR)와 크기(PRLR) 레지스터를 PCB에 저장함 (MMU에 저장되는 경우도 있다고 함)

### 물리주소 계산
- addr(p, d)를 요청하게되면 페이지 테이블에서 p에 해당하는 프레임 넘버 f를 찾음
- f * S + d 가 실제 물리 메모리 주소 (S는 페이지 크기)

### 발전 기술
### 1. 페이지 공유
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fk32F0%2FbtqNqBSHrhg%2FqYfF3FkXdagcndWnTjuiLk%2Fimg.png)
- 사용자 문맥 중 Code 부분은 변경되어선 안되는데 이를 reenterent 코드라고 함
- reenterent 코드가 만약 한 프로세스에 의해 먼저 메인메모리에 탑재 되었다면 다른 여러 프로세스가 공유
- 페이지 테이블을 활용해 해당 프레임에 대해 페이지 테이블이 동일한 번호를 기록

### 2. 메모리 보호
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FTOfYo%2FbtqNllRPxGM%2FdxdZAMIYk6tY89ZKiH4UeK%2Fimg.png)
- 페이지 테이블에 보호비트를 마련함으로서 메모리 보호 기능을 구현
- 페이지 테이블에 valid/invalid를 표시하는 bit를 두고 활용
- 특정 이유로 해당 페이지를 접근하려는 시도가 있으면 트랩을 걸어 오류로 처리, <br/>
  메모리의 보호된 영역을 접근하는 것을 방지


### 3. 페이지테이블 효율성 증가
- Hierarchical Paging - 페이지 테이블을 분할하여 저장
- Hashed Page Table - 32bit보다 큰 공간을 다룰 때 사용/ 페이지 번호를 해싱을 통해 관리
- Inverted Page Table - 페이지테이블 값이 아닌 Index값을 통해 물리주소를 계산 (공유 어려움)

## 2. 세그멘테이션
> - 실제로 사용자(개발자)는 자신의 프로그램을 페이지 모음으로 인식하기보단, <br/>
>   함수는 함수대로, 자료구조는 자료구조대로 각각 단위 별로 메모리 상에 존재하는 것으로 <br/>
>   인식함이러한 것을 **세그먼트** 라고 정의
> - 세그멘테이션은 세그먼트를 물리 메모리 운영에 반영하는 것
> - 크기가 가변적이기 때문에 페이징처럼 미리 분할해놓을 수 없음
> - 페이징 기법보다 공유와 보호가 용이

### 세그먼트 생성
- 컴파일러가 코드를 분석하면서 세그먼트를 식별하고 생성
- 생성된 세그먼트는 사용자 문맥을 구성하는 code, heap, stack, c library, global variables 등이 세그먼트로 저장
- 해당 로더블 파일(실행 가능한 - 메모리에 적재 가능한 파일)이 로더에 의해 메모리에 적재되면서 <br/>
  세그먼트에 번호가 매겨짐 (세그먼트 번호)
- 세그먼트의 논리주소는 세그먼트 번호 s와 오프셋 d로 이루어짐

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbd1Bzn%2FbtqAKur3847%2F8GEFV56KPoiUJx4wO7k1S0%2Fimg.png)

## 3. 페이지드 세그멘테이션
> Segmentation과 Paging 가각의 장점을 한꺼번에 얻기 위해 두 기법을 동시에 사용
>
> 먼저 Segmentation을 수행하고 각 Segment 별로 paging을 수행
>
> 기존 Segment Table 구조에서 Base Address 대신 해당 Segment의 Page Table Address 를 저장
>
> 기존 Segment Table 구조에서 Bound 값 대신 해당 Segment의 Page 개수를 저장.

![](https://media.geeksforgeeks.org/wp-content/uploads/20210415165132/SegmentedPaging.png)

### 장점
- 외부 단편화 해결
- 세그먼트 단위 공유 및 보호 가능

### 단점
- 한번의 메모리 참조 당 extra 메모리 참조가 2번 총 3번이 필요
- 성능이 더욱 저하되는 문제가 발생

<br/><br/>
---

## 가상메모리

> 단순 메모리 할당은 전체 프로그램을 적재해야 하지만,
>
> 가상 메모리 기법은 일부만 적재하고도 프로그램 수행이 가능
>
> 사상 단위는 페이지테이블을 사용하는 페이징 기법과 똑같이 페이지
>
> 프로그램의 일부만 적재되는 만큼 논리공간의 크기보다 실제 필요 물리공간의 크기가 작음
>
> 프로그램은 적은 수의 프레임을 번갈아가면서 이용하기 때문에 메모리 활용도가 증대
>
> Paging과 Paged Segmentation 기법을 가상 메모리에 적용 - **demand paging** & demand segmentation
>
> Paged Segmentation은 내부적으로 page로 처리되므로 실제로는 demand paging 사용

### 원리
- 페이지가 프레임 중 적절한 곳에 적재된 후, 해당 프레임 번호가 페이지 테이블에 기록
- 페이지 테이블에 프레임 번호뿐만 아니라, 유효/무효(vaild/indvalid) 정보도 기록될 수 있다.
- 유/무효 비트를 활용해 메인메모리에 적재되는 페이지만 유효표시
- 유무효 표시비트를 전환해가면서 필요한 부분을 로드

## 요구 페이징 *Demand Paging*

> 프로세스를 수행하는 중 페이지를 요청하였는데, 해당 페이지가 적재되어있지 않을 때,
>
> (페이지 테이블의 유무효 비트를 통해 판단) 페이지 부재 *Page fault* 발생
>
> Page Fault 발생 시, 페이지를 적재하는 것을 **요구 페이징**이라고 함

### 타당성

- 프로세스 구동 시, 전체보단 일부를 필요로 하기 때문 (**참조의 지역성** *locality of reference*)
- 부분적재는 page fault 발생빈도(page faul rate)를 낮추는 것이 핵심
- 부분적재 관련 이슈로 페이지 대치 알고리즘, 프레임 개수, 페이지 크기, 쓰레싱 등이 존재

### 과정

1. Page fault 발생
2. MMU가 Trap(Page fault trap) 발생시킴
3. 운영체제에서 Page fault 처리 루틴 진입
4. 보조기억장치에서 페이지를 찾은 후, 메모리에 적재
5. 페이지 테이블에서 해당 페이지의 유/무효 비트를 valid로 변경

### 페이지 교체 *Page Replacement*
> 3번의 과정에서 memory에 free frame이 없을 경우 기존에 존재하는 A 페이지를 swap-out 하고 적재해야함
>
> 이 때, A페이지를 결정하는 알고리즘을 페이지 교체 알고리즘이라고 함
>
> 목적 - 가까운 미래에 참조될 가능성이 가장 적은 페이지를 Swap-out 하는것

### 1. 최적 교체 *OPT*

> Frame에 존재하는 page 중 가장 먼 미래에 참조될 페이지를 swap-out
>
> 페이지의 참조 순서를 미리 알고 있어야 하므로 실제 구현이 불가능
>
> 다른 알고리즘들의 성능 비교 용도로 사용됨

### 3. FIFO

> 가장 먼저 메모리에 올라온 page 를 swap-out

### 4. LRU

> *Least Recently Used*
>
> `시간 지역성` - 최근에 참조된 페이지는 가까운 미래에 다시 참조될 가능성이 높음 - 을 사용
>
> 메모리에 적재된 page중 가장 오래동안 참조되지 않은 페이지를 swap-out
>
> 많은 OS가 채택한 효율이 좋다고 평가되는 Algorithm

### 5. LFU

> *Least Frequently Used*
>
> 과거 참조 횟수 *reference count*가 가장 적은 페이지 swap-out

- Incache-LFU : 페이지가 메모리에 적제된 후부터 참조 횟수를 count
- Perfect-LFU : 메모리 적재 여부와 상관 없이, 해당 페이지의 과거 총 참조 횟수 count - 기록 유지 비용이 발생

### 6. MFU

> *Most Frequently Used*
>
> LFU와 반대로 가장 참조횟수가 많은 page를 swap-out
>
> 참조 횟수가 적은 페이지가 앞으로 사용될 가능성이 높다고 판단한 Algorithm

### 6. 쿨럭 Algorithm

> LRU, LFU는 페이지 참조 시각 및 횟수를 SW적으로 유지 및 비교 (PageTable에 기록)
>
> 이와 비교되는 *Clock Algorithm*은 HW의 지원을 통해 운영 오버헤드를 최소화
>
> 대부분의 시스템에서 페이지 교체 알고리즘으로 채택
>
> 오랫동안 참조 되지않은 페이지를 swap-out
>
> LRU와 유사하지만, 가장 오래된 페이지를 교체한다는 보장이 되지 않음

### NUR *Not Used Recently* NRU *Not Recently Used*로 불림

- 메모리의 프레임마다 참조 비트가 존재 - 참조시 1 setting
- 클럭 알고리즘에 의해
  - 참조비트가 1인 페이지는 0으로 바꾼 후 다음 페이지 확인
  - 참조비트가 0인 경우 교체 대상 페이지로 선택

<br/><br/>
---

## 스레싱 *Thrashing*

> Page Fault Rate 가 상승하여 CPU 사용률이 급격히 감소되는 현상

- CPU 이용률이 낮은 경우는 Ready Queue 에 프로세스가 계속해서 있지 않은 경우
- 메모리에 올라온 Process가 너무 적어 모두 I/O 작업을 하며 Ready Queue가 비게 됨
- OS는 이 상황에서 MPD *Multi-Programming-Degree* 를 증가시킴
  - 메모리에 올라가는 프로세스 수를 더 증가시킴
- MPD가 과도하게 상승하면 한 Process에 할당되는 메모리 영역이 지나치게 감소 - Page Fault 발생
  - Page Fault 발생 시, Page를 불러오기 위해 Disk I/O 작업 필요 => CPU를 다른 Process에 이양
  - 이양받은 Process또한 Page Fault 발생 반복
- 이런 상황이 반복되어 CPU 이용률이 떨어지는 경우를 스레싱 *Thrashing* 이라고 함

### 해결방법
- MPD 수치를 감소시킴
- Working Set Algorithm
    > 지역성 집합 *Locality Set*이 메모리에 동시에 올라가도록 보장하는 알고리즘
  - 워킹셋 *Working Set*이 한꺼번에 메모리에 적재될 수 있는 경우에만 프로세스에게 메모리 할당
  - 위 상황이 되지 않으면 메모리 전체 Swap-out

```
Q. OPT 알고리즘이 왜 실현이 불가능한가?
A.
모든 페이지들에 대해 페이지가 속해있는 프로세스와 해당 프로세스의 우선순위 그리고 그 우선순위에 의해
프로세스가 Ready Queue에 삽입되는 순서를 계산하여 앞으로 가장 오랫동안 사용되지 않을 프로세스를 교체해야함
이 작업이 새로운 프로세스가 Excute 상태가 될때마다 발생됨
부하가 너무 큼
```

### 다음 내용 참조 : https://developer-ping9.tistory.com/70

### 이미지 출처
- https://goodmilktea.tistory.com/35
- http://joyqul.blogspot.com/2014/01/ios-ch-8-memory-management-strategies.html
- https://www.geeksforgeeks.org/paged-segmentation-and-segmented-paging/