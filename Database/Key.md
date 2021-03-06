# Key

### Key란?

> Table에서 검색이나 정렬, 수정등의 관리를 위해 각Tuple을 구분할 수 있는 기준이 되는 Attribute를 의미
>
> key의 종류에 따라 후보키, 기본키, 대체키, 슈퍼키, 외래키가 존재

### 용어

- 유일성 : 튜플 중 해당 속성 또는 속성의 집합이 중복되는 튜플이 없어야 함
- 최소성 : 꼭 필요한 속성으로만 구성

## 종류
<br/>

### 후보키 *Candidate Key*
> 릴레이션을 구성하는 속성들 중에서 Tuple을 유일하게 식별할 수 있는 
> 
> 속성 또는 속성들의 부분 집합을 의미
> 
> 하나의 릴레이션에는 반드시 하나 이상의 후보기가 존재해야함
> 
> 모든 튜플에 대해서 유일성과 최소성을 만족

### 기본키 *Primary Key*
> 릴레이션에서 튜블을 유일하게 식별 가능한 key 
> 
> 후보키의 조건을 만족하며 NULL 값을 가질 수 없음 - 개체무결성 조건
> 
> 모든 튜플에 대해서 유일성과 최소성, 개체 무결성을 만족

### 대체키 *Alternate Key*
> 후보키가 둘 이상일 때, 기본 키를 제외한 나머지 후보키들
> 
> 보조키라고 불리기도 함

### 슈퍼키 *Super Key*
> 릴렝션의 속성들 중 특정 속성들로 이루어진 집합
> 
> 슈퍼키로 지정된 속성들의 집합은 해당 집합을 통해 튜플을 유일하게 식별할 수 있어야함
> 
> 유일성을 만족시키지만, 최소성을 만족하지 않음

### 외래키 *Foreign Key*
> 릴레이션간 관계를 맺기 위해 사용되는 키
> 
> 릴레이션 A와 릴레이션 B가 관계를 가지고 있다고 가정할 떄,
> 
> 릴레이션 A의 외래키는 릴레이션 B의 기본키여야 함 (참조 무결성)

## 무결성 *Integrity*
> 도메인, 키, 관계성 등의 데이터베이스 요소가 훼손되지 않고 정확성과 안전성을 나타내는 것으로 
> 
> 무결성 제약조건은 정확성과 안정성을 유지하기 위한 제약조건

1. NULL 무결성 
- 릴레이션의 특정 속성 값이 NULL이 될 수 없도록 하는 규정

2. 고유 *Unique* 무결성 
- 릴레이션의 특정 속성에 대해 각 튜플이 갖는 속성 값들이 서로 달라야 한다는 규정

3. 도메인 *Domain* 무결성
- 특정 속성의 값이 그 속성이 정의된 도메인에 속한 값이어야 한다는 규정
 
4. 키 *Key* 무결성
- 하나의 릴레이션에는 적어도 하나의 키가 존재해야한다.

5. 관계 *Relationship* 무결성
- 릴레이션에 어느 한 튜플의 삽입 가능 여부 또는 한 릴레이션과 다른 릴레이션의 튜플들 사이의 관계에 대한 적절성 여부를 지정한 규정

6. 참조 *Referential* 무결성
- 외래키(Foreign Key) 값은 NULL이거나 참조 릴레이션의 기본키(Primary Key) 값과 동일해야 한다는 규정

7. 개체 *Entity* 무결성
- 기본 릴레이션의 기본키를 구성하는 어떤 속성도 NULL 일 수 없다는 규정