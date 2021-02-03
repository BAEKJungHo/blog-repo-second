---
title: "SecurityConfig 설정하기"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 설정파일을 만드는 방법에 대해서 배워보겠습니다."
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티 설정파일을 만드는 방법에 대해서 배워보겠습니다.

# SecurityConfig

스프링 시큐리티 설정 파일을 만들기 위해서는 다음과 같은 작업이 필요합니다.

1. 설정 파일임을 나타내는 `@Configuration` 애노테이션 설정
2. 스프링 시큐리티의 웹 보안 기능 및 초기화 설정을 담당하는 `WebSecurityConfigurerAdapter` 상속
3. 웹 시큐리티 기능을 활성화 시키는 `@EnableWebSecurity` 애노테이션 설정

위 세 가지를 거쳐야 자신만의 스프링 시큐리티 설정 파일을 만들 준비가 된 것입니다. 

## WebSecurityConfigurerAdapter

`WebSecurityConfigurerAdapter` 는 `스프링 시큐리티의 웹 보안 기능 및 초기화 설정을 담당` 하며 세가지 `configure` 메서드를 통해서 세부적인 설정을 할 수 있습니다.

- configure(WebSecurity)
    - 스프링 시큐리티의 필터 연결을 설정하기 위한 오버라이딩이다.
- configure(HttpSecurity)
    - 세부적인 보안설정을 할 수 있게한다.
- configure(AuthenticationManagerBuilder)
    - 사용자 세부 서비스를 설정하기 위한 오버라이딩이다.

## @EnableWebSecurity

`@EnableWebSecurity` 애노테이션을 사용하면 핸들러 메서드에서 `@AuthenticationPrincipal` 애노테이션을 사용하여 UserDetailsService 에서 리턴한 객체를 파라미터로 직접 받아서 사용할 수 있습니다.

```java
@GetMapping("/")
public String index(@AuthenticationPrincipal Account account) {
    // 생략
}
```

## Sample

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
       http.authorizeRequests()
               .antMatchers("/user").hasRole("USER")
               .anyRequest().permitAll();
       http.formLogin();

    }

}
```

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)