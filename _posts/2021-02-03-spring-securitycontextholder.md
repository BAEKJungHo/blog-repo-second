---
title: "SecurityContextHolder"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "스프링 시큐리티 SecurityContextHolder 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

스프링 시큐리티 인증 모델의 핵심인 SecurityContextHolder 에 대해서 배워보겠습니다.

# SecurityContextHolder

SecurityContextHolder 는 스프링 시큐리티 Authentication Model 의 핵심(core) 입니다.

![holder](/images/posts/202102/holder.JPG)

> 출처 : [Spring security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5)

`SecurityContextHolder` 는 SecurityContext 로 이루어져 있으며 `SecurityContext` 는 `Authentication` 으로 이루어져 있습니다. Authentication 은 `Principal, Credential, Authorities` 로 이루어져 있습니다.

즉, SecurityContextHolder 는 Spring Security 가 인증한 사용자의 세부 정보를 저장하는 곳입니다. 

- Setting SecurityContextHolder

```java
SecurityContext context = SecurityContextHolder.createEmptyContext(); 
Authentication authentication = new TestingAuthenticationToken("username", "password", "ROLE_USER"); 
context.setAuthentication(authentication);

SecurityContextHolder.setContext(context); 
```

Multi Thread 에서 race condition(경합 상태)를 방지하기 위해서는 SecurityContextHolder.getContext().setAuthentication(authentication) 코드 보다는 새로운 SecurityContext 를 생성하는 것이 좋습니다.

- Access Currently Authenticated User 

```java
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
String username = authentication.getName();
Object principal = authentication.getPrincipal();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```

여기서 `GrantAuthority` 는 "ROLE_USER", "ROLE_ADMIN" 등 Principal 이 가지고 있는 `권한` 을 나타냅니다. 인증 이후, `인가 및 권한 확인`할 때 이 정보를 참조합니다.

```java
/*
* SecurityContextHolder 는 ThreadLocal 을 사용하므로 아래 정보는 한 스레드에 유지된다.
* SecurityContextHolder 에 있는 SecurityContext 를 가져와서 그 안에 있는 Authentication 을 꺼내오고
* Authentication 에 있는 Principal 객체를 가져온다. 그 객체의 타입은 UserDetails 이다.
*/
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
UserDetails userDetails = (UserDetails) authentication.getPrincipal();
```

여기서 SecurityContextHolder 의 가장 중요한 특징이 나왔습니다. 바로 SecurityContextHolder 의 기본 전략은 `ThreadLocal` 이라는 것입니다. 따라서 SecurityContextHolder 는 ThreadLocal 을 사용하기 때문에 인증된 사용자의 정보를 어디서든지 꺼내서 쓸 수 있습니다.

## SecurityContext

- Authentication 이 저장되는 보관소
- ThreadLocal 에 저장되어 어디든 꺼내서 쓸 수 있음
- 인증이 완료되면 HttpSessionSecurityContextRepository 를 통해서 HttpSession 기반으로 SecurityContext 를 저장해 둔다. 따라서 전역적인 참조가 가능하다.

### SecurityContext 의 객체 저장방식

기본 전략은 `ThreadLocal`이라고 배웠습니다. 그 외에도 다른 전략이 존재합니다.

- MODE_THREADLOCAL : 스레드당 SecurityContext 를 할당한다. (Default Strategy)
- MODE_INHERITABLETHREADLOCAL : 메인 스레드와 자식 스레드에 대해야 동일한 SecurityContext 를 유지한다.
- MODE_GLOBAL : 응용 프로그램에서 단 하나의 SecurityContext 를 가진다.

### ThreadLocalSecurityContextHolderStrategy

```java
final class ThreadLocalSecurityContextHolderStrategy implements SecurityContextHolderStrategy {
    private static final ThreadLocal<SecurityContext> contextHolder = new ThreadLocal();

    ThreadLocalSecurityContextHolderStrategy() {
    }

    public void clearContext() {
        contextHolder.remove();
    }

    public SecurityContext getContext() {
        SecurityContext ctx = (SecurityContext)contextHolder.get();
        if (ctx == null) {
            ctx = this.createEmptyContext();
            contextHolder.set(ctx);
        }

        return ctx;
    }

    public void setContext(SecurityContext context) {
        Assert.notNull(context, "Only non-null SecurityContext instances are permitted");
        contextHolder.set(context);
    }

    public SecurityContext createEmptyContext() {
        return new SecurityContextImpl();
    }
}
```

## Flow

![holder2](/images/posts/202102/holder2.JPG)


## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)