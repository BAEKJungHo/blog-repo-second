---
title: "UserDetails 와 UserDetailsService"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 UserDetails 와 UserDetailsService 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티의 UserDetails 와 UserDetailsService 에 대해서 배워 보겠습니다.

# UserDetails

Security 에서 사용자의 정보를 담는 인터페이스가 UserDetails 인터페이스입니다.

해당 인터페이스를 구현하여 Custom UserDetails 를 만들 수도 있습니다.

# UserDetailsService

UserDetailsService 는 DaoAuthenticationProvider 에서 사용자 이름, 암호 및 사용자 이름과 암호로 인증하기 위한 기타 특성을 검색하는 데 사용됩니다. Spring Security 는 UserDetailsService 의 인메모리 및 JDBC 구현을 제공합니다.

사용자 지정 사용자 세부 정보 서비스를 bean 으로 표시하여 사용자 지정 인증을 정의할 수 있습니다. 예를 들어 CustomUserDetailsService 가 UserDetailsService 를 구현한다고 가정하면 다음에서 인증을 사용자 정의합니다.

UserDetailsService 를 구현하는 Custom UserDetailService 가 있다면 아래와 같이 설정할 수 있습니다.

```java
@Bean
CustomUserDetailsService customUserDetailsService() {
    return new CustomUserDetailsService();
}
```

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)