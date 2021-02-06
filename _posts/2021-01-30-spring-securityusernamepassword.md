---
title: "UsernamePasswordAuthenticationFilter"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "사용자 로그인시 인증 처리를 하는 필터인 UsernamePasswordAuthenticationFilter 에 대해서 배워보겠습니다."
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

폼 로그인(Form Login) 방식에서 실제로 사용자가 로그인을 하게 되면 인증 처리가 이루어지는데, 그 인증을 처리하는 필터가 바로
`UsernamePasswordAuthenticationFilter` 이며, 이 필터에 대해서 배워보겠습니다.

# UsernamePasswordAuthenticationFilter

> UsernamePasswordAuthenticationFilter : 폼 로그인을 처리하는 인증 필터

## 인증 매커니즘

UsernamePasswordAuthenticationFilter 는 username 과 password 를 HttpServletRequest 로 부터 읽기 위한 세 가지 매커니즘을 제공합니다.

- Form Login
- Basic Authentication
- Digest Authentication

## Form Login 인증 동작 방식

먼저 인증 요청이 올때 어떻게 유저가 로그인 페이지로 리다이렉트 하는지 배워 보겠습니다.

![formlogin](/images/posts/202102/formlogin.jpg)

> 출처 : [Spring security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)

1. 가장먼저 유저는 인증되지 않은 리소스(/private) 으로 요청을 합니다.
2. `FilterSecurityInterceptor`가 인증되지 않은 요청에 대해 `AccessDeniedException` 예외를 던집니다.
3. 그 후 사용자가 인증되지 않았으므로 `ExceptionTranslationFilter` 는 인증을 시작하고 Authentication EntryPoint가 구성된 로그인 페이지로 리디렉션을 전송합니다. 대부분의 경우 AuthenticationEntryPoint는 LoginUrlAuthenticationEntryPoint의 인스턴스입니다.
4. 그 후 브라우저가 리디렉션된 로그인 페이지를 요청합니다.
5. 그리고 로그인 페이지를 랜더링 합니다.

-------------------------------------------------

사용자가 입력한 username 과 password 가 제출되면 `UsernamePasswordAuthenticationFilter` 를 통해 인증을 시도합니다.

![formlogin2](/images/posts/202102/formlogin2.jpg)

> 출처 : [Spring security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)

-------------------------------------------------

`UsernamePasswordAuthenticationFilter` 의 더 자세한 인증 방식은 다음 그림과 같습니다.

![usernamepassword](/images/posts/202102/usernamepassword.jpg)

사용자의 로그인 요청이 오면 가장먼저 `UsernamePasswordAuthenticationFilter`를 거치게 됩니다. 그리고 `AntPathRequestMatcher` 를 통해서 요청 정보가 매핑되는지 확인하고, 매핑 되지 않을 경우 `chain.doFilter` 를 동작하게됩니다.
요청 정보가 매핑 된다면 username 과 password 로 `Authentication`을 만들고 `AuthenticationManager` 를 사용하여 인증을 시도합니다. `AuthenticationManager(ProviderManager)` 는 여러 `AuthenticationProvider` 를 사용하여 인증을 시도하는데, 그 중에 `DaoAuthenticationProvider` 는 `UserDetailsService` 를 사용하여 `UserDetails` 정보를 가져와 사용자가 입력한 password 와 비교합니다. 


> 즉, 실제 인증 처리는 AuthenticationProvider 가 처리합니다. 

인증에 성공하면 `user 정보`와` Authorities(권한) 정보`를 저장하여 다시 `AuthenticationManager` 에게 리턴합니다. `AuthenticationManager` 는 `AuthenticationProvider` 에게 받은 최종적인 인증 객체인 `Authentication `을 필터에게 전달합니다. 그리고 이 필터는 `Authentication` 을 `SecurityContext` 에 저장합니다. 그리고 마지막으로 `SuccessHandler` 를 통해 성공 후 작업을 처리하게 됩니다.

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)