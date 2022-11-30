---
title: Spring Security + JWT Token + OAuth2 (1)
categories: [Learn, Spring Boot]
tags: [spring boot, spring security, oauth2, jwt]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2022-11-30 17:50:00 # 페이지의 마지막 수정일
sitemap :
  changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
  priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5) 
---

## Spring Security

Spring 프레임 워크를 사용하면 로그인 구현에서 많이 사용하는 Spring Security에 대해서 알아보자

### Srping Security란?

Spring Security는 Spring 프레임워크 기반에서 작동하는 인증과 인가, 권한 부여 등 과 같은 보안 처리를 해주는 프레임 워크다

### 꼭 필요한가?

프로젝트 환경에 따라 꼭 필요하진 않지만 사용하면 개발의 편의가 증가하고 필요한 부분만 커스터마이징 하여 사용 할 수도 있기 때문에 고려해볼 사항이다.

### 작동원리

<img width="1426" alt="아키텍쳐" src="https://lgm1995.github.io/assets/img/learn/springboot/springsecurity/springsecurity_architecture.png">


비전공자 입장에서 최대한 쉽게 이해한 전체적 흐름은 이렇다.

1. 사용자가 로그인 폼에 대하여 아이디와 비밀번호로 로그인을 요청한다.

2. Filter에 의해 Authentication Frilter 중 디폴트 UsernamePasswordAuthentication Filter가 아이디와 비밀번호를 가져온 후 인증을 위한 토큰 UsernamePasswordAuthentication Token 을 만들어 AuthenticaionManager를 구현한 ProviderManager에게 위임한다.

3. ProviderManager는 AuthenticationProvider(s)를 돌며 위임 받은 객체를 처리할 수 있는 Provider를 찾고 DB에 정보가 일치하는게 있는지 요청한다.

4. Provider는 UserDetailsService를 통해 repository를 활용하여 유저 정보를 매칭하고 UserDetails에 정보를 담아서 넘긴다.

5. 인증된 정보 객체(Authentication)를 인메모리인 SecurityContext에 저장하고 사용자에게 인증된 session ID를 전송한다.

6. 이후 사용자는 요청시 쿠키(JSESSIONID)에 session-id 정보를 담아서 요청한다.

### 장점

1. 사용자의 정보를 서버에 저장하고 있기 때문에 관리가 좋다.
2. 사용자의 정보를 서버(session에서)와 클라이언트(cookie에서) 모두 저장하고 있기 때문에 상태를 유지할 수 있다.

### 단점

1. 서버에서 인메모리에 사용자 정보를 담기 때문에 사용자가 많아지면 서버에 부하가 커진다.

해당 단점을 보완하기 위해 JWT Token 방식이 많이 사용된다. 다음글에서 JWT Token에 대해 알아보자
  
>##### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!