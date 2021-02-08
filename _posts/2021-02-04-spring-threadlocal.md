---
title: "ThreadLocal"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "ThreadLocal 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티 SecurityContextHolder 의 기본 전략인 ThreadLocal 에 대해서 배워보겠습니다.

## ThreadLocal

- Java.lang 패키지에서 제공하는 스레드 범위 변수. 즉, 스레드 수준의 데이터 저장소
- 같은 스레드 내에서만 공유
    - 따라서 같은 스레드라면 해당 데이터를 메서드 매개변수로 넘겨줄 필요 없음
    - SecurityContextHolder 의 기본 전략
    - SercurityContextHolder 내부에 감춰져있는 Principal 에서 로그인한 User 정보가 스레드 단위로 유지된다.
    - 서블릿은 클라이언트 요청 1개마다 하나의 스레드를 생성해서 요청을 처리하기 때문에, 1명 = 1스레드 라고 생각하면 된다.


```java
public class AccountContext {
    private static final ThreadLocal<Account> ACCOUNT_THREAD_LOCAL = new ThreadLocal<>();

    public static void setAccount(Account account) {
        ACCOUNT_THREAD_LOCAL.set(account);
    }

    public static Account getAccount() {
        return ACCOUNT_THREAD_LOCAL.get();
    }
}
```

```java
/**
 * 로그인이 필요한 페이지
 * @param model
 * @param principal
 * @return
 */
@GetMapping("/dashboard")
public String dashboard(Model model, Principal principal) {
    model.addAttribute("message", "Hello " + principal.getName());
    
    // ThreadLocal 에 account 정보 세팅
    AccountContext.setAccount(accountRepository.findByUsername(principal.getName()));
    
    sampleService.dashboard();
    return "dashboard";
}
```

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)