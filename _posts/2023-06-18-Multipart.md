---
title: Multipart를 활용한 파일 업로드에 대해서
categories: [Learn, Programming, Http]
tags: [http, file, multipart, multipart/form-data]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-05-19 09:48:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

> 이번 시간엔 파일 업로드를 할 때 많이 사용되는 Multipart/form-data에 대해서 알아본다.

## 파일 업로드

회사에서 Legacy 코드들을 개선작업중이다. 그 중 하나가 post 요청에 원하는 데이터를 QueryString으로 보내던 작업을
VO를 활용하여 간단하게 body로 받아서 처리하도록 개선하고 있다.

그 와중에 파일 업로드 부분에 대해서 Multipart/form-data에 대해서 알게 되었는데 이 데이터를 body로 받아보려 삽질하다가 알게 된 내용을 전달한다.

## Multipart란?

사전적 의미는 메시지를 여러 부분으로 분할하여 전송하는 인터넷 프로토콜이다.
주로 파일 전송에 사용되는 프로토콜로 사용 이유와 특징을 알아본다

### Multipart/form-data

기존에도 HTML에서 파일을 업로드하는 방법은 있었다 바로 input type의 file을 이요하는 것이다.
이때 이미지를 form으로 전송하게 되면 헤더에는 관련하여 content-type을 image/jpeg등 이미지로 받아
해당 데이터를 body에 담아 보낸다.
이렇게 일반적인 http 요청에 대한 Content-type은 apllication/x-www-form-urlencoded이다.

근데 이렇게 하면 문제가 발생한다.

만약 회원이 이미지를 업로드하면서 보내는 요청에 회원의 정보나 이미지에 대한 제목이나 내용 등 다양한 정보가 같이 전송되어야 한다면 한번에 요청으로 이 모든것을 해결 할 수 가 없다.

그 이유는 content tpye이 다르기 때문이다.

그래서 등장한 것이 multipart 타입이다.

멀티 파트는 메시지의 경계값을 가지고 있어 구분이 가능하며 각 부분에 대한 헤더와 데이터를 가지고있다.
따라서 Content-type, Content-Disposition, Content-Length 등의 정보를 포함하고 있으며, 각각의 데이터를 형식에 맞춰 인코딩하여 전송한다.

인코딩의 정보를 다르게 설정할 수 있기 때문에 다양한 데이터를 한번에 post요청에 보내는 것이 가능해진다.


https://lena-chamna.netlify.app/post/http_multipart_form-data/

## Spring에서의 Multipart 지원

Spring Multipart를 지원하여 MultipartResolver를 설정하게 되면 스프링 MVC에서 Multiart형식의 데이터를 다룰 수 있다.

https://jihwan-study.tistory.com/83

https://velog.io/@qudtjs0753/Spring-multipartform-data-%EC%97%85%EB%A1%9C%EB%93%9C-%EA%B4%80%EB%A0%A8


## Spring Boot에서 Mulitpart 사용

instagram 예제 +

https://gofnrk.tistory.com/36

RequestParm + RequestPart + RequestBody

https://middleearth.tistory.com/35

### 마무리

애초에 바이너리 타입의 데이터는 텍스트가 아닌 이진형식이라 RequestBody로 매핑할 수 없었다.
RequestParam으로 한 건이나 다중 건에 대한 파일을 MultipartFile 객체를 사용하여 받을 수 있고 RequestBody가 필요한 경우
RequestPart를 사용할 수 있다. (RequestPart를 사용하여도 만약 content-type이 multipart/form-data가 없을 경우 RequestBody처럼 동작)


>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!







