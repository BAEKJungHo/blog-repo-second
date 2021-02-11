---
title: "Authentication"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 Authentication 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티의 Authentication(인증)에 대해서 배워 보겠습니다.

# Authentication

Authentication 은 `당신이 누구인지를 증명하는 것`을 의미합니다. 

- 사용자의 인증 정보를 저장하는 토큰 개념
- 인증 시 id 와 password 가 인증을 위해서 사용된다.
- 인증 후 최정 결과가 SecurityContext 에 저장되어 전역적으로 참조가 가능하다.
- 구조
    - principal : 사용자 아이디 혹은 User 객체를 저장
    - credentials : 사용자 비밀번호, 인증 후에는 유출되지 않도록 이 정보가 지워집니다.
    - authorities : 인증된 사용자의 권한 목록
    - details : 인증 부가 정보
    - Authenticated : 인증 여부

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)