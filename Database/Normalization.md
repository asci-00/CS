# 정규화 *Normalization*

> DB에서 데이터 관리의 효율성 증대를 위해 테이블 간에 중복된 데이타를 허용하지 않는다는 것을 목표로 함
>
> 무결성 *Integrity*을 유지할 수 있음 <br/><br/>
>
> 논리적 설계 단계에서 발생할 수 있는 종속으로 인한 삭제,갱신,삽입 이상(Anomaly)현상의 문제점을 해결하기 위해,
>
> 속성들 간의 종속 관계를 분석하여 여러 개의 릴레이션으로 분해하는 과정

### 목차
- 용어
- 정규화 단계
- 반정규화

<br/><br/>

---
## 용어

### 함수 종속

> 1. 릴레이션 R에서 X 와 Y의 속성의 부분집합으로 정의
> 2. X의 값이 Y 값을 정함 (X의 값을 알면 Y의 값을 식별 가능)
> 3. 이를 Y는 X에 함수적 종속이라고 함
> 4. X를 결정자, Y를 종속자, X->Y 로 표현

### 완전 함수적 종속

> 종속자가 기본키에만 종속
>
> 기본키가 속성의 집합일 경우, 해당 집합에 종속되어야 함

### 부분 함수적 종속

> 기본키가 아닌 속성에 종속되거나, 기본키를 구성하는 속성 중 일부에만 종속되는 경우

### 이행적 함수 종속

> 릴레이션에서 X, Y, Z 3개의 속성이 존재
>
> X->Y이며, Y->Z일때, X->Z가 성립될 때, 이를 이행적 함수 종속이라고 정의
>
> X를 알면 Y를 알고, 이를 통해 Z를 식별할 수 있는 경우

### 다치 종속


<br/><br/>

---
## 정규화 단계

### 제 1정규형 1NF

> 도메인 종속 제거
>
> 모든 도메인이 원자값 *Atomic Value* 만으로 구성되도록 하는 정규형

도메인이 원자값이 아닌 릴레이션

| 이름 | 취미 |
|---|---|
| USER1 | INTERNET |
| USER2 | MOVIE, BOOK |
| USER3 | MUSIC, SHOPPING |

원자값으로 분리

| 이름 | 취미 |
|---|---|
| USER1 | INTERNET |
| USER2 | MOVIE |
| USER2 | BOOK |
| USER3 | MUSIC |
| USER3 | SHOPPING |

### 제 2정규형 2NF

> 제 1정규형을 만족하는 릴레이션에 대하여
>
> 부분 함수적 종속 제거 (완전 함수적 종속으로 변환)

부분 함수적 종속 관계를 가지는 릴레이션

| 이름 | 강좌 | 강의실 | 성적 |
|---|---|---|---|
| USER1 | 컴퓨터공학 | 공학관 1호 | 3.4 |
| USER2 | 자료구조 | 공학관 2호 | 4.3 |

> 기본키가 (이름, 강좌) 일 경우
>
> 성적 속성은 기본키에 종속적이지만,
>
> 강의실은 기본키의 구성인 강좌에만 종속적임

| 이름 | 강좌 | 성적 |
|---|---|---|
| USER1 | 컴퓨터공학 | 3.4 |
| USER2 | 자료구조 | 4.3 |

| 강좌 | 성적 |
|---|---|
| 컴퓨터공학 | 공학관 1호 |
| 자료구조 | 공학관 2호 |

> 릴레이션을 분할하여 제 2정규형 만족

### 제 3정규형 3NF

> 제 2정규형을 만족하는 릴레이션에 대하여
>
> 이행적 함수 종속 제거

이행적 함수 종속 관계를 가지는 릴레이션

| 이름 | 강좌 | 수강료 |
|---|---|---|
| USER1 | 컴퓨터공학 | 20000 |
| USER2 | 컴퓨터공학 | 20000 |
| USER3 | 자료구조 | 50000 |

> 이름 -> 강좌 / 강좌 -> 수강료 관계로 인해 이름->수강료 관계를 가짐

| 이름 | 강좌 |
|---|---|
| USER1 | 컴퓨터공학 |
| USER2 | 컴퓨터공학 |
| USER3 | 자료구조 |

| 강좌 | 수강료 |
|---|---|
| 컴퓨터공학 | 20000 |
| 자료구조 | 50000 |

> 릴레이션을 분할하여 제 3정규형 만족

### BCNF 정규형

> 3 정규형을 만족하는 릴레이션에 대하여
>
> 모든 결정자는 후보키 라는 조건을 만족

| 이름 | 강좌 | 담당교수 |
|---|---|---|
| USER1 | 컴퓨터공학 | PRO1 |
| USER2 | 컴퓨터공학 | PRO2 |
| USER3 | 자료구조 | PRO3 |
| USER3 | 컴퓨터공학 | PRO2 |

> 기본키 (이름, 강좌)
> 1. 이름의 경우 - USER3의 경우 강좌에서 자료구조, 컴퓨터공학 / 담당교수에서 PRO3, PRO2를 가지므로 결정자가 아님
> 2. 강좌의 경우 - 컴퓨터공학의 담당교수가 PRO1, PRO2 가 존재하며, USER1, USER2, USER3 모두 컴퓨터공학을 가지므로 결정자가 아님
> 3. 담당교수의 경우 - PRO1이 컴퓨터공학, PRO2가 컴퓨터공학, PRO3이 자료구조로 정해지므로, 담당교수->강좌의 조건을 만죡

> 담당교수가 결정자이므로 후보키가 되기위해 아래와 같이 테이블을 분할해야함

| 이름 | 담당교수 |
|---|---|
| USER1 | PRO1 |
| USER2 | PRO2 |
| USER3 | PRO3 |
| USER3 | PRO2 |

> 기본키 (이름, 담당교수)

| 강좌 | 담당교수 |
|---|---|
| 컴퓨터공학 | PRO1 |
| 컴퓨터공학 | PRO2 |
| 자료구조 | PRO3 |

>기본키 (강좌, 담당교수)

```
이 외에도 4, 5 정규화가 존재하지만, 3정규화 이후부터는 잘 쓰이지 않으며
특수한 정규화방식임
```


<br/><br/>

---
## 반정규화

> 시스템의 성능 향상, 개발 및 운영의 편의성 등을 위해 정규화된 데이터 모델을
>
> `통합`, `중복`, `분리`하는 과정으로, 의도적으로 정규화 원칙을 위배하는 행위

### 특징

- 시스템의 성능이 향상되고 관리 효율성을 증가 But 데이터의 일관성 및 정합성이 저하
- 과도한 반정규화는 오히려 성능을 악화시킴
- 일관성과 무결성(정규화) vs 성능과 단순화(반정규화)
- 테이블 통합, 테이블 분할, 중복 테이블 추가, 중복 속성 추가 등이 존재