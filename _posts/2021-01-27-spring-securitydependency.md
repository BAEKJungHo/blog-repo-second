---
title: "스프링 시큐리티 의존성 추가하기"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 의존성을 추가하는 방법과 일어나는 일들에 대해서 배워보겠습니다."
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티 의존성을 추가하는 방법과 일어나는 일들에 대해서 배워보겠습니다.

# 스프링 시큐리티 의존성 추가하기

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

시큐리티 의존성을 추가하고 톰캣을 시작하면 콘솔에 아래와 같은 문자열이 찍힙니다.

`Using generated security password: b6047e41-e261-1234-123a-9e6abc11b059`

스프링 시큐리티는 기본적으로 위와 같은 임의의 패스워드를 제공하고, ID 는 `user`라는 문자열로 제공합니다.

## 스프링 시큐리티 의존성 추가 시 일어나는 일들

- 서버가 기동되면 스프링 시큐리티의 `초기화 작업 및 보안 설정`이 이루어진다.
- 별도의 설정이나 구현을 하지 않아도 기본적인 웹 보안 기능이 현재 시스템에 연동되어 작동한다.
    - 모든 요청은 인증이 되어야 자원에 접근이 가능하다.
    - 인증 방식은 폼 로그인 방식과 httpBasic 로그인 방식을 제공한다.
    - 기본 로그인 페이지를 제공한다.
    - 기본 계정 한 개를 제공한다. (id : user / pw : 콘솔에 찍히는 랜덤 문자열)

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)