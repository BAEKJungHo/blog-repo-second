---
title: "SecurityContextPersistenceFilter"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 SecurityContextPersistenceFilter 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티 SecurityContext 를 세션에 저장하는 필터인 SecurityContextPersistenceFilter 에 대해서 배워 보겠습니다.

# SecurityContextPersistenceFilter

SecurityContextPersistenceFilter 는 SecurityContext 의 객체의 생성, 저장 및 조회의 역할을 담당하는 필터입니다.

![persist](/images/posts/202102/persist.JPG)

SecurityContextPersistenceFilter 는 새로운 SecurityContext 객체를 생성하여 SecurityContextHolder 에 저장합니다.

익명 사용자의 경우 `AnonymousAuthenticationFilter` 에서 `AnonymousAuthenticationToken` 객체를 `SecurityContext` 에 저장합니다.

인증 시 `UsernamepasswordAuthenticationFilter` 에서 인증 성공 후 `SecurityContext` 에 `UsernamepasswordAuthentication` 객체를 저장합니다.
인증이 최종 완료 되면 Sesseion 에 SecurityContext 를 저장합니다.

![persist2](/images/posts/202102/persist2.JPG)

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)