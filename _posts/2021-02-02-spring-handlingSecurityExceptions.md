---
title: "Handling Security Exceptions"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 예외 핸들링에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티에서 예외를 다루는 방법에 대해서 배워보겠습니다.

# Handling Security Exceptions

`ExceptionTranslationFilter` 는 `AccessDeniedException` and `AuthenticationException` 에 대한 예외를 다룹니다. `ExceptionTranslationFilter` 는 `FilterChainProxy` 에 등록된 필터 중 하나입니다.

![exceptionfilter](/images/posts/202102/exceptionfilter.JPG)

> 출처 : [Spring security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5)

1. 가장 먼저 ExceptionTranslationFilter 는 FilterChain.doFilter(request, response)를 호출하여 애플리케이션의 나머지를 호출합니다.
2. 사용자가 인증되지 않았거나 AuthenticationException 인경우 Authentication 을 시작합니다.
    - The SecurityContextHolder is cleared out
    - The HttpServletRequest is saved in the RequestCache. When the user successfully authenticates, the RequestCache is used to replay the original request.
    - The AuthenticationEntryPoint is used to request credentials from the client. For example, it might redirect to a log in page or send a WWW-Authenticate header.
3. AccessDeniedException 인 경우에는  AccessDeniedHandler 가 호출되어 집니다.

- ExceptionTranslationFilter pseudocode

```java
try {
    filterChain.doFilter(request, response); 
} catch (AccessDeniedException | AuthenticationException ex) {
    if (!authenticated || ex instanceof AuthenticationException) {
        startAuthentication(); 
    } else {
        accessDenied(); 
    }
}
```

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)