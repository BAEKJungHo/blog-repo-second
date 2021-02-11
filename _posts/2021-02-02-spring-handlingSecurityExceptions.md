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

## 정리

- Spring Security 에서 인증과, 인가 과정에서 예외가 발생할 경우 ExceptionTranslationFilter 가 이를 담당합니다.
- AuthenticationException 예외가 발생한 경우라면, 다시금 인증을 하도록 이를 유도하게 되고
- AccessDeniedException 예외가 발생했다면 익명 사용자인지 확인, 익명사용자라면 인증을 유도하며, 익명사용자가 아니라면 accessDeninedHandler 에게 이를 위임한다.
- 모든 Security 필터에서 발생하는 AuthenticationException, AccessDeniedException 처리를 담당하는 것은 아니다.
- UsernamePasswordAuthenticationFilter 에서 발생한 예외의 경우에는 부모클래스인 AbstractAuthenticationProcessingFilter 에서 이를 처리한다.

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [ncucu Spring Security](https://pupupee9.tistory.com/112)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)