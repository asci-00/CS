# Browser Process

![image](https://lh3.googleusercontent.com/W57FWvNCUMnv8HBOoTI-CKcyAqXfE7E9kTZ4MbZXlbqR-sDFd4Tmm8Em4u0d5kgJAg=w720-rwa)

> 브라우저의 대략적인 프로세스 정리 

### 1. HTTP Request + Response

> 브라우저에서 제공된 주소창에 url을 입력하여 서버에 자원을 요청
> 
> HTTP 1.1이전은 keep-alive 기능이 없어 모든 요청에 대해 각각 connection이 생성됨
> 
> HTTP 2.0이후부터는 keep-alive 설정에 의해 일정한 조건에 의해 connection이 유지됨
> 
> (하나의 connection에서 여러 요청이 동시에 발생) 

![image2](https://mdn.mozillademos.org/files/13827/HTTPMsgStructure2.png)

- Browser는 HTTP Request를 생성하여 Server로 요청
- Server는 HTTP Request에 대한 응답으로 HTTP Response를 생성하여 Browser에 전송
- Browser는 응답받은 HTML 문서에서 추가적인 Server에 대한 요청이 존재할 경우 <br/>(Resource - image, font, Javascript, css ..) Rendering을 진행하면서 동시에 Server에게 Resource 요청 <br/>(병렬적으로 동작)

### 참고
- [TCP UDP](https://github.com/asci-00/CS/blob/main/Network/TCP_UDP.md)
- [DNS 이름 해결](https://github.com/asci-00/CS/blob/main/Network/HTTP_Request.md)
- [HTTP HTTPS](https://github.com/asci-00/CS/blob/main/Network/HTTP_HTTPS.md)

### 2. HTML Parsing + DOM Creation

> 브라우저 Rendering Engine에 의해 HTML 문서를 Parsing하여 DOM을 생성한다.

1. Server의 HTTP Response는 바이트코드로 contents를 응답함
2. contents는 Byte -> Word -> Token -> Node -> DOM 과정을 거쳐서 변환됨
   1. 이 과정에서 바이트 형태의 HTML 문서는 meta tag의 charset attribute에 의해 지정된 encoding 방식을 따름
   2. HTML Document는 HTML 요소들의 집합이며, HTML 요소는 중첩 관계를 가짐<br/>이는 Tree 형태로 표현이 되며, 이 자료구조를 `DOM`이라고함

### [참고](https://github.com/asci-00/CS/tree/main/Browser)