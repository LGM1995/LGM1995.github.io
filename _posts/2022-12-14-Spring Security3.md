---
title: Spring Security + JWT Token + OAuth2 (3)
categories: [Learn, Spring Boot]
tags: [spring boot, spring security, oauth2, jwt]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2022-12-14 10:13:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

## OAuth2

지난 포스팅에 JWT에 대해서 알아보았다. 이번 게시글에서는 소셜로그인이 가능하게 도와주는 OAuth2에 대해서 알아본다.

### OAuth2(Open Authorization2)란?

2006년 부터 트위터와 구글이 정의한 인가절차의 개방형 표준으로 기존 OAuth1.0 에서 개선된 버전

### OAuth2의 구조

* Resource Server : OAuth 제공자로서 구글, 트위터, 페이스북, 카카오톡 등 과 같이 이미 사용자 정보를 가지고 요청에 따른 데이터(Resource)를 넘겨줄 서버
* Resource Owner : 애플리케이션에서 Resource 자원을 요청할 주체로 실제 로그인을 진행할 사람
* Client : Resource 활용할 실제 애플리케이션
* Authorization Server : 실제 클라이언트(애플리케이션)와 Resource Owner 사이의 사용자 인증과정을 진행해줄 서버(현재 나의 프로젝트의 경우 Spring Security를 사용한 백엔드 서버)

### OAuth2 + Spring Security 동작 방식

<img width="1426" alt="아키텍쳐" src="https://lgm1995.github.io/assets/img/learn/springboot/springsecurity/springsecurity_OAuth2.0.png">

* (A) : 클라이언트(애플리케이션)가 Resource Owner에게 권한 부여를 요청(구글, 페이스북, 카카오톡 등 사용할 소셜의 로그인을 진행)
* (B) : 권한(아이디, 비밀번호, 소셜타입)등을 리턴
* (C) : 부여받은 권한(Authorization Grant)을 활용하여 실제 애플리케이션 서버에 사용자가 있는지 비교
* (D) : 사용자 인증이 완료되면 Access Token을 발급
* (E) : Access Token으로 Resource Server에 필요한 자원을 요청
* (F) : 요청된 자원을 전달

### 장점

마이데이터의 활용으로 제 3의 앱으로 부터 서비스 요청이 쉽기 때문에 새로운 신규 사용자 유입이 간편하다.

보안적인 측면에서 높은 보안 수준을 제공할 수 있다.

### 단점

구현하기까지의 과정이 복잡하며 여러 SNS 계정이 연동이 가능한 경우 CSRF 공격을 통해 공격자의 계정과 연결될 가능성이 있다.

지금까지 Spring Security와 JWT 그리고 OAuth2.0에 대하여 아주 간단하게 정리해 보았다.

지금까지 포스팅된 글로만으로 프로젝트에 적용 시키는것은 쉽지 않을 것이다(예제코드와 조금더 자세한 내용 공부 필요)
JWT의 경우 Access Token과 Refresh Token을 어떻게 발급하고 어디에서 저장하여 이를 활용할 것인지 생각해보고,
OAuth2.0의 경우 승인 방식에 따른 프로토콜 프로세스를 좀 더 살펴보길 바란다.

>##### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!
