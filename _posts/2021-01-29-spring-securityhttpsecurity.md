---
title: "HttpSecurity"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "세부적인 보안 설정을 위한 API 를 제공하는 HttpSecurity 에 대해서 배워보겠습니다."
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

세부적인 보안 설정을 위한 API 를 제공하는 HttpSecurity 에 대해서 배워보겠습니다.

# HttpSecurity

`HttpSecurity` 는 WebSecurityConfigurerAdapter 를 상속받은 클래스에서 `configure(HttpSecurity)` 메서드를 구현함으로써
스프링 시큐리티 관련 세부적인 보안 설정을 할 수 있게 해줍니다.

- WebSecurityConfigurerAdapter 의 configure(HttpSecurity) 메서드

```java
protected void configure(HttpSecurity http) throws Exception {
    this.logger.debug("Using default configure(HttpSecurity). If subclassed this will potentially override subclass configure(HttpSecurity).");
    ((HttpSecurity)((HttpSecurity)((AuthorizedUrl)http.authorizeRequests().anyRequest()).authenticated().and()).formLogin().and()).httpBasic();
}
```

- http.authorizeRequests() 
    - 보안 설정을 하겠다는 의미, 시큐리티 처리에 `HttpServletRequest` 를 이용한다는 것을 의미
- http.anyRequest.authenticated() 
    - 어떠한 요청에도 인증을 요구한다는 의미
- http.formLogin().and().httpBasic() 
    - formLogin 방식과 httpBasic 방식을 지원한다는 의미

세부적인 보안 설정을 제공하는 API 는 크게 `인증 API`와 `인가 API`로 나뉩니다.

## 인증 API

- http.formLogin()
- http.logout()
- http.csrf()
- http.httpBasic()
- http.SessionManagement()
- http.RememberMe()
- http.ExceptionHandling()
- http.addFilter()

## 인가 API

- http.authorizeRequests()
    - .antMatchers(/admin)
    - .hasRole(USER)
    - .permitAll()
    - .authenticated()
    - .fullyAuthentication()
    - .access(hasRole(USER))
    - .denyAll()

## 디버깅

```java
protected final HttpSecurity getHttp() throws Exception {
    if (this.http != null) {
        return this.http;
    } else {
        AuthenticationEventPublisher eventPublisher = this.getAuthenticationEventPublisher();
        this.localConfigureAuthenticationBldr.authenticationEventPublisher(eventPublisher);
        AuthenticationManager authenticationManager = this.authenticationManager();
        this.authenticationBuilder.parentAuthenticationManager(authenticationManager);
        Map<Class<?>, Object> sharedObjects = this.createSharedObjects();
        // HttpSecurity 객체 생성
        this.http = new HttpSecurity(this.objectPostProcessor, this.authenticationBuilder, sharedObjects);
        if (!this.disableDefaults) {
            // 여기서 11개의 세부적인 보안기능을 설정할 수 있는 API 를 제공하는 것을 알 수있다.
            // 이 기능은 HttpSecurity 가 제공한다.
            ((HttpSecurity)((DefaultLoginPageConfigurer)((HttpSecurity)((HttpSecurity)((HttpSecurity)((HttpSecurity)((HttpSecurity)((HttpSecurity)((HttpSecurity)((HttpSecurity)this.http.csrf().and()).addFilter(new WebAsyncManagerIntegrationFilter()).exceptionHandling().and()).headers().and()).sessionManagement().and()).securityContext().and()).requestCache().and()).anonymous().and()).servletApi().and()).apply(new DefaultLoginPageConfigurer())).and()).logout();
            ClassLoader classLoader = this.context.getClassLoader();
            List<AbstractHttpConfigurer> defaultHttpConfigurers = SpringFactoriesLoader.loadFactories(AbstractHttpConfigurer.class, classLoader);
            Iterator var6 = defaultHttpConfigurers.iterator();

            while(var6.hasNext()) {
                AbstractHttpConfigurer configurer = (AbstractHttpConfigurer)var6.next();
                this.http.apply(configurer);
            }
        }

        this.configure(this.http);
        return this.http;
    }
}
```

위 코드에서 exceptionHandling 을 타고 들어가서 ExceptionHandlingConfigurer 클래스의 configure 메서드를 보면 ExceptionTranslationFilter 를 생성하여 사용하는것을 알 수 있습니다.

- ExceptionHandlingConfigurer

```java
public void configure(H http) {
    AuthenticationEntryPoint entryPoint = this.getAuthenticationEntryPoint(http);
    ExceptionTranslationFilter exceptionTranslationFilter = new ExceptionTranslationFilter(entryPoint, this.getRequestCache(http));
    AccessDeniedHandler deniedHandler = this.getAccessDeniedHandler(http);
    exceptionTranslationFilter.setAccessDeniedHandler(deniedHandler);
    exceptionTranslationFilter = (ExceptionTranslationFilter)this.postProcess(exceptionTranslationFilter);
    http.addFilter(exceptionTranslationFilter);
}
```

> 위 디버깅을 토대로 알 수 있는 것은 시큐리티 사용자 보안설정 11 개의 API 들이, 각각의 설정 클래스를 호출하는 것을 알 수 있고, 각각의 설정 클래스가 필터를 생성하고 있다는 것을 알 수 있다.

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)