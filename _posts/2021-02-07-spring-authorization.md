---
title: "Authorization"
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

스프링 시큐리티의 Authorization(인가)에 대해서 배워 보겠습니다.

# Authorization

Authorization 은 `당신에게 무엇이 허가됬는지를 증명하는 것`을 의미합니다. 

> 인가(Authorization)란 인증(Authentication)된 사용자가 해당 자원에 접근할 수 있는 권한이 있는지 확인 하는 것이다.

## 스프링 시큐리티가 지원하는 권한 계층

- 웹 계층
    - URL 요청에 따른 메뉴 혹은 화면단위의 레벨 보안
- 서비스 계층
    - 화면 단위가 아닌 메서드 같은 기능 단위의 레벨 보안
- 도메인 계층(Access Control List, 접근제어목록)
    - 객체 단위의 레벨 보안
    
## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)