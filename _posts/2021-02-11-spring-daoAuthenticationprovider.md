---
title: "DaoAuthenticationProvider"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 DaoAuthenticationProvider 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티의 DaoAuthenticationProvider 에 대해서 배워 보겠습니다.

# DaoAuthenticationProvider

DaoAuthenticationProvider 는 AuthenticationProvider 의 구현체 입니다.

![logo](/images/posts/202102/daoAuth.JPG)

> DaoAuthenticationProvider 는 UserDetailsService 를 사용하여 UserDetails 정보를 가져와 사용자가 입력한 password 와 비교합니다.

- number 1. The authentication Filter from Reading the Username & Password passes a UsernamePasswordAuthenticationToken to the AuthenticationManager which is implemented by ProviderManager.
- number 2. The ProviderManager is configured to use an AuthenticationProvider of type DaoAuthenticationProvider.
- number 3. DaoAuthenticationProvider looks up the UserDetails from the UserDetailsService.
- number 4. DaoAuthenticationProvider then uses the PasswordEncoder to validate the password on the UserDetails returned in the previous step.
- number 5. When authentication is successful, the Authentication that is returned is of type UsernamePasswordAuthenticationToken and has a principal that is the UserDetails returned by the configured UserDetailsService. Ultimately, the returned UsernamePasswordAuthenticationToken will be set on the SecurityContextHolder by the authentication Filter.

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)