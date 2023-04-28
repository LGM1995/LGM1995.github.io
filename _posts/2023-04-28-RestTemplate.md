---
title: OBJECT DAO, DTO, VO, ENTITY의 차이
categories: [Learn, Spring, RestTemplate]
tags: [resttemplate, http, restful, api]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-04-29 10:48:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

# RestTemplate란

오늘은 Spring 3.0부터 지원하는 RestTemplate에 대해서 알아본다.

웹 어플리케이션의 작동을 보면 클라이언트는 서버에 요청을 하면 서버는 응답하여 클라이언트에게 데이터를 보여준다.

<img width="1426" src="https://lgm1995.github.io/assets/img/learn/spring/cl-ser.png">

이때 대부분의 서버는 XML이나 JSON 형식의 데이터를 리턴하게 되는데 특히 많이 쓰이는 JSON(JavaScript Object Notation)은 키-값 형태로 인간이 쉽게 이해할 수 있는 텍스트 형식의 개방형 표준 포맷이다.

<img width="1426" src="https://lgm1995.github.io/assets/img/learn/spring/json.png">

## 그럼 RestTemplate는 뭐냐?

<img width="1426" src="https://lgm1995.github.io/assets/img/learn/spring/cle-ser-ser.png">

Spring 3.0 부터 지원하는 Http 통신을 유용하게 다룰 수 있는 템플릿이다.

이 말을 이해하기 위해 이것이 생성되기 전의 원시적인 HTTP 통신을 찾아보았다.

이전의 Spring은 서버와 통신하기 위해서 URLConnection을 사용했는데 사용방법은 아래의 코드와 같다.

(Post 요청에 대한 예시 소스코드)
```
public class URLConnectionPostJsonExample {
   public static void main(String args[]) throws Exception {
      URL url = new URL("https://www.example.com/api/create");
      HttpURLConnection conn = (HttpURLConnection) url.openConnection();
      conn.setRequestMethod("POST");
      conn.setDoOutput(true);
      conn.setRequestProperty("Content-Type", "application/json");

      String jsonString = "{\"name\":\"John\",\"age\":30,\"city\":\"New York\"}";
      OutputStream os = conn.getOutputStream();
      os.write(jsonString.getBytes());
      os.flush();
      os.close();

      BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
      String inputLine;
      while ((inputLine = br.readLine()) != null) {
         System.out.println(inputLine);
      }
      br.close();
   }
}
```

사용 방법을 보면 요청 자원 url을 지정하고 메서드타입, 미디어 타입 등 필요한 세팅을 마친 후 getOutputStream()으로 OutputStream 객체를 얻어 JSON 문자열을 작성한 후 getInputStream()으로 응답을 읽는 작업을 수행한다.

그러나 이 방식은 최대 단점이 있다. 응답 상태가 200임을 가정하기에 응답 코드에 따라 Exception이 터질 수 있다.

또한 타임아웃을 설정할 수 없으며 쿠키를 제어할 수 없다는 문제점이 있다.

그래서 이러한 문제점을 고치기 위해 HttpClient가 등장하였다.

Apache 재단에서 지원하는 라이브러리로 Maven 3.x 이상의 버전부터 사용이 가능하며 Dependency에 의존성을 추가하여 사용한다.

(Post 요청에 대한 예시 소스코드)
```
public class HttpClientPostJsonExample {
   public static void main(String args[]) throws Exception {
      CloseableHttpClient client = HttpClients.createDefault();
      HttpPost httpPost = new HttpPost("https://www.example.com/api/create");

      // JSON 데이터 생성
      String jsonString = "{\"name\":\"John\",\"age\":30,\"city\":\"New York\"}";
      StringEntity entity = new StringEntity(jsonString);
      httpPost.setEntity(entity);
      httpPost.setHeader("Content-type", "application/json");

      CloseableHttpResponse response = client.execute(httpPost);
      try {
         HttpEntity result = response.getEntity();
         String resultString = EntityUtils.toString(result);
         System.out.println(resultString);
      } finally {
         response.close();
      }
   }
}
```

이 방식을 사용하면 에러코드 Response Codes(응답코드)와 상관없이 읽어내어 Exception이 발생하지 않으 쿠키를 제어할 수 있고 타임아웃도 설정이 가능하다.

RestTemplate는 내부적으로 URLConnection, HttpClient 등의 라이브러리를 추상화 하여 제공하기 때문에 간편하게 Rest방식의 API를 호출할 수 있다.

## 근데 왜 클라이언트와 서버가 직접 통신하면 되지 서버끼리 통신을 할까?

우선 나의 회사의 경우의 예시로 들어본다.

<img width="1426" src="https://lgm1995.github.io/assets/img/learn/spring/cle-ser-ser2.png">

클라이언트는 Server2에 연결된 DB의 정보를 원한다. 그러나 Server2는 외부 접근을 허용하지 않으며 Server1과는 인가된 포트를 사용하여 통신을 한다.

이때 클라이언트는 요구 데이터를 얻기위해 Server1에게 요청을 하고 Server1이 Server2에게 요청으로 얻은 데이터를 Json으로 클라이언트에게 제공한다.

이처럼 방화벽이나 외부 접근을 막아야 할 때 또는 그 이외의 다양한 상황으로 클라이언트로 부터 직접 서버 접근을 막아야 하는 경우 서버끼리 통신을 해야한다.

만약 회사의 서버가 여러대가 있고 잘 정리된 RestTemplate가 있다면 새로운 API 생성시 작성해야할 코드가 줄고 개발 시간이 늘어날 것이다.

## RestTemplate의 원리

<img width="1426" src="https://lgm1995.github.io/assets/img/learn/spring/resttemplate.png">

 1. 어플리케이션이 RestTemplate를 생성하고, URI, HTTP메소드 등의 헤더를 담아 요청한다.
 2. RestTemplate 는 HttpMessageConverter 를 사용하여 requestEntity 를 요청메세지로 변환한다.
 3. RestTemplate 는 ClientHttpRequestFactory 로 부터 ClientHttpRequest 를 가져와서 요청을 보낸다.
 4.  ClientHttpRequest 는 요청메세지를 만들어 HTTP 프로토콜을 통해 서버와 통신한다.
 5.  RestTemplate 는 ResponseErrorHandler 로 오류를 확인하고 있다면 처리로직을 태운다.
 6.  ResponseErrorHandler 는 오류가 있다면 ClientHttpResponse 에서 응답데이터를 가져와서 처리한다.
 7.  RestTemplate 는 HttpMessageConverter 를 이용해서 응답메세지를 java object(Class responseType) 로 변환한다.
 8.  어플리케이션에 반환된다.

## RestTemplate 예제 코드

(Post 요청에 대한 예시 소스코드)
```
RestTemplate restTemplate = new RestTemplate();

HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);

JSONObject requestBody = new JSONObject();
requestBody.put("name", "John");
requestBody.put("age", 30);
requestBody.put("city", "New York");

HttpEntity<String> request = new HttpEntity<>(requestBody.toString(), headers);

String url = "http://example.com/api/endpoint";
ResponseEntity<String> response = restTemplate.postForEntity(url, request, String.class);

if (response.getStatusCode() == HttpStatus.OK) {
    String responseBody = response.getBody();
    // do something with the response body
} else {
    // handle error
}
```
(restTemplate 메서드 종류)

<img width="1426" src="https://lgm1995.github.io/assets/img/learn/spring/resttemplate-mathud.png">

### 마무리

RestTemplate는 간단하고 사용하기 쉬우며, 다양한 HTTP 요청을 처리하는 기능하지만 단점이 존재한다.

 1. 동기식 요청 처리 : RestTemplate는 기본적으로 동기식으로 처리되기 때문에, 요청이 느리게되거나 응답이 지연될 경우 애플리케이션 성능에 영향을 미칠 수 있다.(비동기 처리를 위한 AsyncRestTemplate가 생김)
 2. 쓰레드 안전성 : 동시 다발적인 요청 처리를 위해 별도의 동기화 처리가 필요하다.
 3. 런타임 오류 : 런타임 오류를 처리하기 위해 예외 처리를 사용해야하며 이를 처리하지 않았을 경우 애플리케이션 안정성에 영향을 미칠 수 있다.

현재 RestTemplate는 Spring에서 더 이상 지원을 하지 않을 예정(삭제는 아님)라고 하며 현재는 WebClient 나 WebFlux 등 다른 대안들도 존재한다.

따라서 회사의 시스템에 맞춰서 개발하는 것이 제일 중요하다고 볼 수 있고 만약 회사에 정형화 된 템플릿이나 가이드가 없다면 개발하는데 시간이 많이 들고 개발 시간 확보하는 것이 어려울 것이다.

>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!









