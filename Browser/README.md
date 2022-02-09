# Browsers

_How Browsers Work: Behind the scenes of modern web browsers_

---

> 브라우저는 사용자가 선택한 자원을 서버에 요청하고 브라우저에 표시하는 것을 주 목적으로 함.
> 자원의 주소는 URI에 의해 정해짐

### Browser의 주요 구성 요소

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdab612f-8e61-4df1-ab7b-a7068fc14606/Untitled.png)

_Chrome은 다른 Browser과 달리 각 Tab마다 별도의 렌더링 엔진 인스턴스를 유지함_

_각 탭이 독립된 프로세스로 처리됨_

```html
### 렌더링 엔진 ### 게코 *Gecko* : Internet explorer, Firefox 웹킷 *Webkit* : Chrome, Safari
```

## Rendering Engine

> request에 대응하는 response 내용을 브라우저 화면에 표시

- HTML XML 문서와 이미지를 표시할 수 있음
- Plugin이나 확장 기능을 이용하여 PDF 등의 다른 유형도 표시 가능
- HTML parser와 CSS parser에 의해 parsing되어 DOM · CSSOM 트리 생성
- DOM + CSSOM ⇒ Render Tree ( Render Tree를 기반으로 웹 페이지를 표시 )

![Rendering Engine 동작 과정](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8c3866fc-2c03-4e60-9717-f85d2b117414/Untitled.png)

Rendering Engine 동작 과정

> 렌더링 엔진은 통신으로부터 요청한 문서의 내용을 얻는 것으로 시작
> 문서의 내용은 보통 8KB 단위로 전송

DOM 트리를 바탕으로 Render 트리를 구축할 때, 이미 완성된 DOM 트리부터 Render 트리 구축을 수행
`**병렬실행**`

>

Render Tree는 색상 또는 면적과 같은 시각적 속성이 있는 사각형을 포함하며,
정해진 순서대로 화면에 표시

- 렌더 트리 배치 `**좌표값**`: 구축된 렌더 트리를 바탕으로 각 요소를 화면 특정 위치에 배치
- 렌더 트리 그리기 ( 화면 표시 )

![웹킷 동작 과정](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/56c8e295-8749-494f-894b-d3985b0354ef/Untitled.png)

웹킷 동작 과정

![모질라의 게코 렌더링 엔진 동작 과정](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4b9d1dd8-aad7-4755-a717-3bf3bc420055/Untitled.png)

모질라의 게코 렌더링 엔진 동작 과정

# 파싱과 DOM 트리 구축

_간단한 개념정리_

---

## 파싱

> `**파싱**` 브라우저가 코드를 이해하고 사용할 수 있는 구조로 변환하는 것을 의미
> 파싱 결과는 문서 구조를 나타내는 노드 트리 (= 파싱 트리 _Parsing tree_ = 문법 트리 _Syntax tree_ )

### 문법

> 파싱 Parsing을 하기 위해서는 대상 문서가 언어 또는 형식, 구문 규칙에 따라야 하는데,
> 이것을 `**문맥 자유 문법**` 이라고 함

### 어휘 분석 vs 구문 분석

> 어휘 분석: 자료를 \**토큰*으로 분해하는 과정 ( 공백과 줄 바꿈 등의 의미 없는 문자 제거 )
> 구문 분석: 언어의 구문 규칙을 적용하는 과정 ( 파싱 트리 생성 )

\*토큰: 유효하게 구성된 단위의 집합체 ( 용어집 = 단어 )

>

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/91d0c394-0c5d-4aef-b166-901cde70656a/Untitled.png)

- 파서는 어휘 분석기로부터 새 토큰을 받아 구문 분석을 수행
  - 규칙에 적합하면 토큰에 해당하는 노드를 파싱 트리에 추가 후 과정 반복
  - 규칙에 적합하지 않다면 토큰을 내부적으로 저장하고 일치하는 규칙이 발견될 때까지 요청
    But, 맞는 규칙이 없는 경우 예외로 처리 → 문서가 유효하지 않고 Syntax Error를 포함함을 의미

### 파서

> 하향식 파서: 상위 구조로부터 일치하는 부분을 탐색
> 상향식 파서: 낮은 구조에서부터 점차 높은 부분으로 탐색

### 변환

> 컴파일러는 파싱 트리를 생성하고 이를 기계 코드 문서로 변환함

---

# 그렇다면 Browser는?

## HTML Parser

> HTML Parser는 HTML Markup을 Parsing Tree로 변환

### 💡HTML Parsing은 `문맥 자유 문법`이 아니다.

> 전통적인 Parser는 HTML에 적용할 수 없다. HTML은 파서가 요구하는 문맥 자유 문법에 의해 쉽게 정의되지 않음
> 파싱은 CSS와 Javascript를 파싱하는 데 사용됨

### ❓XML 그리고 XHTML도 파서가 존재하는데, HTML 이 없는 이유는?

HTML이 더 유연 - HTML은 암묵적으로 태그에 대한 생략이 가능하기 때문에 유연한 문법을 제공한다.

이 때문에 공식적인 문법으로 작성하기 어렵게 만들며 HTML은 파싱하기 어렵고 전통적인 구문 분석이 불가능 하기 때문에 문맥 자유 문법에 포함되지 않음

## DTD _Document Type Definition_

> SGML 계열 언어의 정의를 이용한 HTML 정의를 위한 공식적인 형식
> 허용되는 모든 요소 및 그들의 속성 그리고 중첩 구조에 대한 정의를 포함

- 여러 버전의 DTD가 존재하며, 엄격한 혁식은 명세를 따르지만,
  다른 형식은 낡은 브라우저에서 사용된 마크업을 지원

### DOM _Document Object Model_

> 문서 객체 모델의 준말으로, HTML 문서의 객체 표현 (외부 - Javascript에서 사용)
> 브라우저 환경에서 트리의 최상위 객체는 document

[https://www.w3.org/DOM/DOMTR](https://www.w3.org/DOM/DOMTR) 에 명세되어있음

>

DOM은 마크업과 1:1 관계를 맺는다.

```html
<html>
  <body>
    <p>Hello HTML</p>
    <div><img src="example.png" /></div>
  </body>
</html>
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/99e9f169-af34-4f4e-ac78-37a58801908e/Untitled.png)

## ❓그래서 HTML Parsing은 어떻게 동작하는가

_앞서 설명한바와 같이 아래와 같은 이유로 HTML은 일반적인 하향식 또는 상향식 파서로 파싱이 되지 않음_

1. 언어의 유연한 문법
2. 잘 알려져 있는 HTML 오류에 대한 브라우저의 관용
3. 변경에 의한 재파싱 - 일반적으로 소스는 파싱하는 동안 변하지 않지만, HTML에서 document.write을 포함하는 스크립트 태그는 토큰을 추가할 수 있기 때문에 실제로는 입력 과정에서 파싱이 수정됨

**브라우저는 이를 해결하기 위해 HTML 파싱을 위한 별도의 파서를 생성함**
[_https://html.spec.whatwg.org/multipage/parsing.html_](https://html.spec.whatwg.org/multipage/parsing.html)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/95a9b73d-d865-4b70-8d59-2a40a11cd10c/Untitled.png)

파싱 알고리즘: `토큰화` 알고리즘+ `트리 구축` 알고리즘

```html
<html>
  <body>
    Hello world
  </body>
</html>
<!--입력예제-->
```

![입력 예제의 토큰화 ( 토큰화 알고리즘 )](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cf985fe5-55e1-4472-91e4-5bc376bf169d/Untitled.png)

입력 예제의 토큰화 ( 토큰화 알고리즘 )

![입력 예제의 트리 구축 ( 트리 구축 알고리즘 )](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/44a4f84d-237a-44ec-8693-35d4be2a5580/Untitled.png)

입력 예제의 트리 구축 ( 트리 구축 알고리즘 )

### 파싱이 끝난 이후

_문서파싱 이후 실행되어야 하는 `**“지연” 모드 스크립트**`를 파싱_

문서 상태는 `완료` 로 전환 `로드` 이벤트 발생 ( 자세한 내용: [HTML5의 토큰화 알고리즘과 트리 구축](https://html.spec.whatwg.org/multipage/syntax.html) )

## 💡유효하지 않은 문법 처리

> HTML에서 유효하지 않은 태그를 삽입하거나, 문법적 오류가 존재하는 문서를 만들 시,
> 에러가 발생하지 않은 이유

**HTML 파서는 관습적으로 잘못된 문법에 대한 코드를 수정한다.**

_HTML5 명세는 이러한 요구사항 일부를 명세 & 웹킷은 HTML 파서 클래스 시작 부분에 주석으로 요약_

---

- 파서가 임의로 요소를 추가해서는 안됨
- 임의의 태그 안쪽에 추가하려는 태그가 금지된 태그일 경우 일단 허용된 태그를 먼저 닫고 금지된 태그는 외부에 추가
- HTML 제작자에 의해 뒤늦게 요소가 추가될 수 있고 생략 가능한 경우도 존재
  ( HTML, HEAD, BODY, TBODY, TR, TD, LI 태그 )
- 인라인 요소 내부에 블록 요소가 있는 경우 부모 블록 요소를 만날 때까지 모든 인라인 태그를 닫음
  - 이런 방법으로 해결되지 않을 시, 태그를 추가하거나 무시할 수 있는 상태가 될 때까지 요소를 닫음

### 오류 처리 사례

**BR태그 오류**

> \<br> 태그와 \</br> 태그가 혼용되어 사용되었을 때 오류 처리

_internet explorer, firefox와 호환성을 가지기 위해 웹킷은 </br>을 <br>으로 간주한다._

```jsx
if(t->isCloseTag(brTag) && m_document->inCompatMode()) {
    reportError(MalformedBRError);
    t->beginTag = true;
}
//오류는 내부적으로 처리되어 사용자에게 표시되지 않음
```

**테이블 구성 오류**

> 표 안에 또 다른 표가 th 또는 td 셀 내부에 있지 않은 것을 의미 ( 어긋난 표 )

_웹킷은 이를 표의 중첩을 분해하여 형제 요소가 되도록 처리_

```html
<table>
  <table>
    <tr>
      <td>inner table</td>
    </tr>
  </table>
  <tr>
    <td>outer table</td>
  </tr>
</table>
<!-- 어긋난 표 -->
```

```html
<table>
  <tr>
    <td>outer table</td>
  </tr>
</table>
<table>
  <tr>
    <td>inner table</td>
  </tr>
</table>
<!-- 수정된 표 -->
```

```jsx
if (m_inStrayTableContent && localName == tableTag) popBlock(tableTag);
// 오류 처리 코드
```

**중첩된 폼 요소**

> 폼 안에 또 다른 폼을 넣은 경우 안쪽의 폼은 무시

```jsx
if (!m_currentFormElement) {
  m_currentFormElement = new HTMLFormElement(formTag, m_document);
}
```
