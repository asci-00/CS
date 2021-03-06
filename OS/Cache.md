# 캐시 *Cache*
## 목차
- 캐시란
- 지역성
- 사상

<br/><br/>

---

## 캐시란

> 데이터나 값을 미리 복사해 놓는 임시 장소를 의미함
> 
> **어떠한 작업에 대하여 처리 속도 향상을 위해 사용**
> 
> 캐시는 캐시의 접근 시간에 비해 원래 데이터를 접근하는 시간이 오래 걸리는 경우나 
> 
> 값을 다시 계산하는 시간을 절약해야 하는 경우 사용

캐시 메모리, 웹 캐시, DNS 캐시 등등 여러 분야에서 사용됨

- 메인 메모리 <-> CPU 
- 로컬 메모리 <-> 네트워크
- 작업 요청 <-> 부하가 큰 작업

### 1. CPU Cache

> 대용량의 메인 메모리 접근을 빠르게 하기 위해 CPU 칩 내부나 바로 옆에 탑재하는 작은 메모리
> 
> 메인 메모리 (RAM) 와 CPU 중간에서 처리속도의 차이를 완화하기 위해 사용됨

![image](https://user-images.githubusercontent.com/22098393/146766859-6c6e69e8-6c73-470f-8f0e-0c39b44d9cb0.png)

### 2. Web Cache

> HTTP Cache 라고 부름
> 
> 서버 요청에 의한 지연을 줄이기 위해 웹 페이지, 이미지, 기타 유형의 
> 
> 웹 멀티미디어 등의 웹 문서들을 임시 저장하기 위한 기술
> 
> 동일한 서버에 다시 접근할 때에는 근처에 있는 프록시 서버의 웹 캐시에 저장된 정보를 불러오므로 
> 
> 더 빠른 열람을 지원

#### 종류

- Browser Cashes
- Proxy Caches
- GateWay Caches

### Cache 저장소

#### `파일`

- 가장 기본적으로 고려되는 저장소 
-  캐쉬 데이터를 여러 시스템에서 공유하기가 어려움 
-  메모리에 비해 속도가 느림
-  캐쉬 메커니즘을 직접 구현해야 함 

#### `메모리`

- 대용량의 메모리를 지원하는 하드웨어가 늘어나면서 메모리를 캐쉬 저장소로 이용하는 사례도 있음
- Memcached와 같은 솔루션을 이용하면 캐쉬의 생성, 소멸을 솔루션에게 위임 가능
    - 네트워크를 통해서 접근 하는 기능을 지원하기 때문에 단일 캐쉬에 여러 머신에서 엑세스 가능
    - 파일 보다 훨씬 빠르게 데이터를 처리 할 수 있음
- 하드웨어 비용이 비쌈

#### `데이터베이스`

- 자체적인 보안 시스템을 갖춤
- Network를 통해서 접근 할 수 있기 때문에 캐슁 데이터를 공유 가능 
- 메모리 보다 느림

### 동작 방식

1. 캐시 공간을 마련 - 캐시 공간은 상수시간 등 낮은 시간 복잡도(hash map같은)로 접근 가능한 곳을 사용
2. 데이터 요청이 들어오면 원본 데이터에 접근하기 전 캐시 부터 탐색
3. 캐시에 원하는 데이터가 있으면 `Cache hit` 해당 데이터 반환
4. 캐시에 원하는 데이터가 없거나 `Cache miss` 너무 오래되어 최신성을 잃은 `Expireation` 경우 발생시, 
5. 원본 데이터가 있는 곳에 접근하여 데이터를 가져옴
   1. 데이터를 가져오면서 캐시에 해당 데이터를 복사 or 갱신
6. 캐시 공간은 작으므로 공간이 모자라게 되면 안 쓰는 데이터부터 삭제하여 공간을 확보 `Eviction`


<br/><br/>

---

## 지역성


#### 캐시 메모리 관리
- 캐시 메모리 (저장소)의 공간이 한정되어 있으므로, 어떤 데이터를 캐싱할지 결정해야함
- 적중률을 높이기 위해 앞으로 자주 사용될 데이터를 캐싱해야함
- 이 부분에서 `파레토의 법칙`을 적용한 `데이터의 지역성`에 근거하여 캐싱
  - *원인 중 상위 20퍼센트가 전쳉 결과의 80%를 만든다*
  - 모든 코드나 데이터들을 균등하게 Access 하지 않는 특성을 이용

### 시간 지역성
- 최근의 access한 메모리가 가까운 시일 내에 다시 사용될 가능성이 높음

### 공간 지역성
- 특정 메모리에 access 했다면, 해당 주소와 가까운 주소가 순서대로 사용될 가능성이 높음


<br/><br/>

---

## 사상 *Mapping*

> 캐시에서 원하는 데이터를 빠르게 찾기 위해 데이터를 저장하는 기법

- 직접 사상 *Direct Mapping*

![](https://1.bp.blogspot.com/-rl4JsKhfEiw/XYbpNQP9g5I/AAAAAAAACPw/GEFSXbWotVsSia7hB0pFXRcR58ForbHtwCLcBGAsYHQ/s640/%25EC%25BA%25A1%25EC%25B2%2598.JPG)

> 캐시에 데이터를 저장할 때, 지역성의 원리로 인접한 영역을 블록 위치를 의미하는 Tag와 함께 한번에 캐시 메모리에 저장함 ( Block 단위 )
> 
> Word 부분이 블록의 크기를 의미하며, Line 부분이 실제 저장된 데이터를 의미 ( Line 데이터는 각자를 구별하기 위해 앞 3비트를 사용함 )
> 
> 캐시에서 데이터를 찾을 때, Tag 부분을 통해 탐색함

- 연관 사상 *Associative Mapping*
- 집합 연관 사상 *Set-associative Mapping*


### 출처
- 이미지: http://itnovice1.blogspot.com/2019/09/cache.html

