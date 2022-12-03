---
title: Spring Security + JWT Token + OAuth2 (2)
categories: [Learn, Spring Boot]
tags: [spring boot, spring security, oauth2, jwt]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2022-12-03 11:43:00 # 페이지의 마지막 수정일
sitemap :
  changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
  priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5) 
---

## JWT

지난 포스팅에서 Spring Security에 대해서 알아보았다 이번엔 또 다른 인증 방식으로 사용되는 JWT에 대해서 알아본다.

### JWT란?

JWT(Json Web Token)의 약자로 json 형태의 암호화된 token으로 해당 토큰을 통해 인증 및 인가를 진행한다.

### JWT 구조

JWT의 구조는 header.payload.signature로 xxxxx.yyyyy.zzzzz형식으로 생성된다.

 * header : 토큰의 타입(기본값 JWT), alg(해싱 알고리즘)
 * payload : 토큰의 만료시간이나 정보 또는 사용자의 정보를 담고있으며 정보의 한조각을 클레임(claim)이라 하고 "key" : "value"값으로 이루어져있음
 * signature : 백엔드에서 발급되는 인증에 필요한 서명으로 헤더(base64인코딩) + 페이로드(base64인코딩) + 비밀키를 포함하여 암호화 되어있음

### 장점

jwt는 사용자가 로그인에 성공하면 서버는 jwt토큰을 클라이언트에게 전달한다. 이후 사용자는 jwt토큰을 Local Storge나 Session Storage 또는 쿠키에 담아서 가지고 있다가 새로운 요청시 헤더에 토큰을 담아서 보낸다. 서버는 jwt토큰을 확인하여 인증이 확인되면 응답을 보낸다. 기존에 서버가 사용자의 로그인 상태를 유지하기 위해 정보를 저장하고 있던것과 다르게 서버는 항상 무상태(stateless)를 유지할 수 있어 부담이 적고 서버를 확장하기에 용이하다.

### 단점

사용자의 정보를 저장하고 있지 않기 때문에 관리가 어렵다. 토큰이 탈취당하면 만료시간이 될 때까지 방어하기가 힘들다. 따라서 정말 중요한 정보는 payload에 담아선 안되고 보안 방법으로 토큰의 만료시간을 짧게 발행하고 동시에 refresh token이라는 보조 토큰을 만료시간이 길게 발행하여 토큰이 만료시 refresh token을 대조하여 새로 토큰을 발급해주는 방식으로 로그인을 유지시키는 방법이 있다.

### 작동원리

1. 사용자는 로그인 정보(아이디, 비밀번호)를 입력하여 서버에 로그인을 요청한다.

2. JwtTokenProvider를 JwtFilter에 등록하여 해당 필터를 Security에 주입

3. 로그인 정보를 바탕으로 Access Token은 헤더에 Refresh Token은 쿠키에 담아서 보냄

4. 클라이언트는 local storge에 access token을 저장한 뒤 이후 요청마다 header에 access token을 담아서 요청

5. 요청에 대한 응답

만약 access token의 시간이 만료되었다면 refresh token을 대조하여 새로운 access token을 발급해준다.
  
>##### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!