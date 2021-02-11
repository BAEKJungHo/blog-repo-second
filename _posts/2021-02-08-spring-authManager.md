---
title: "AuthenticationManager 와 ProviderManager"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 Authorization 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티의 AuthenticationManager 와 ProviderManager 대해서 배워 보겠습니다.

# AuthenticationManager

스프링 시큐리티에서 인증(Authentication) 은 AuthenticationManager 가 합니다. AuthenticationManager 구현체로는 ProviderManager 를 사용합니다.

```java
public interface AuthenticationManager {
    /**
     * authentication 파라미터에 폼 인증의 경우 사용자가 입력한 Id 와 Password 를 담고 있다.
     */
    Authentication authenticate(Authentication authentication) throws AuthenticationException;
}
```

- 인자로 받은 Authentication 이 유효한 인증인지 확인하고 Authentication 객체를 리턴한다.
- 인증을 확인하는 과정에서 비활성 계정, 잘못된 패스워드, 잠긴 계정 등의 에러를 던질 수 있다.
    - DisabledException : 계정이 비활성화 되어있는 경우
    - LockedException : 계정이 잠긴 경우
    - BadCredentialsException : 패스워드가 일치하지 않는 경우
- 인자로 받은 Authentication
    - 사용자가 입력한 인증에 필요한 정보(username, password)로 만든 객체. (폼 인증인 경우)
    - Authenticaiton
        - Principal : "keesun"
        - Credentials: "123"
- 유효한 인증인지 확인
- 사용자가 입력한 password 각 UserDetailsService 를 통해 읽어온 UserDetials 객체에 들어있는 password 와 일치하는지 확인
- 해당 사용자 계정이 잠겨 있진 않은지, 비활성 계정은 아닌지 등 확인
- Authentication 객체를 리턴
    - Principal : UserDetailsService 에서 리턴한 객체(User)
    - Credentials : 빈값
    - GrantedAuthorities

## ProviderManager

ProviderManager 는 AuthenticationManager 에서 가장 일반적으로 사용되는 구현체입니다.

AuthenticationManager(ProviderManager) 는 여러 AuthenticationProvider 를 사용하여 인증을 시도하는데, 그 중에 `DaoAuthenticationProvider` 는 `UserDetailsService` 를 사용하여 `UserDetails` 정보를 가져와 사용자가 입력한 password 와 비교합니다.

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)