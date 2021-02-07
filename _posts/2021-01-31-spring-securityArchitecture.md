---
title: "Spring Security's High Level Architecture"
layout: post
category: SpringSecurity
tags: [Spring]
excerpt: "서블릿 애플리케이션 기반 스프링 시큐리티의 고 수준 아키텍처에 대해서 배워보겠습니다."
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# Spring Security's High Level Architecture

서블릿 애플리케이션 기반 스프링 시큐리티의 고 수준 아키텍처에 대해서 배워보겠습니다.

## Servlet Filter Chain

스프링 시큐리티는 Servlet Filter 기반으로 이루어지며 Servlet 2.3 버전 부터 등장했습니다. 먼저 서블릿 필터 체인의 방식에 대해서 배워보겠습니다.

![filterchain](/images/posts/202102/filterchain.JPG)

> 출처 : [Spring security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5)

클라이언트가 애플리케이션에 요청을 보내면 서블릿 컨테이너는 요청 URI 경로를 기준으로 `HttpServletRequest` 를 처리해야하는 `Filter` 와 `Servlet`을 포함하는 `FilterChain` 을 생성합니다.

스프링 MVC 애플리케이션에서 서블릿은 `DispatcherServelt` 의 인스턴스를 의미합니다. 하나의 서블릿은 하나의 `HttpServeltRequest` 와 `HttpServletResponse`를 다룰 수 있습니다.

하지만 두개 이상의 필터를 사용하여 아래 처럼 사용할 수 있습니다.


- downstream Filter 또는 서블릿이 호출되지 않도록 합니다. 이 경우 필터는 일반적으로 HttpServletResponse를 씁니다.

- downstream Filter 및 서블릿에서 사용하는 HttpServletRequest 또는 HttpServletResponse 수정


```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    // do something before the rest of the application
    chain.doFilter(request, response); // invoke the rest of the application
    // do something after the rest of the application
}
```

필터는 다운스트림 필터와 서블릿에만 영향을 미치기 때문에 각 필터가 호출되는 순서는 매우 중요합니다.

## DelegatingFilterProxy 과 FilterChainProxy

위에서 배운 서블릿 필터만으로는 스프링에서 정의된 빈을 주입해서 사용할 수 없습니다.

`Servlet Filter` 가 `DelegatingFilterProxy` 으로 요청을 보내면 `DelegatingFilterProxy` 을 통해 스프링 시큐리티가 Filter 기반으로 보안 처리를 할 수 있게 됩니다. 

`DelegatingFilterProxy` 는 서블릿 컨테이너의 라이프사이클과 스프링의 ApplicationContext 사이의 연결을 허용하는 역할을 합니다.

아래 그림을 보겠습니다.

![filterproxy](/images/posts/202102/filterproxy.JPG)

`DelegatingFilterProxy` 는 `springSecurityFilterChain` 이라는 이름을 가진 빈을 `ApplicationContext`에서 찾아 그 빈에게 요청을 위임하며, 스프링은 `FilterChainProxy` 를 빈으로 등록할 때 `springSecurityFilterChain` 이라는 이름으로 등록을 합니다. 따라서 `DelegatingFilterProxy` 가 `FilterChainProxy` 에게 보안 처리를 위임하는 것입니다. 보안처리가 완료되면 최종적으로 `DispatcherServlet` 으로 가서 남은 다른 요청들을 처리하게 됩니다.

`DelegatingFilterProxy` 는 표준 서블릿 컨테이너 메커니즘을 통해 등록할 수 있지만 모든 작업을 필터를 구현하는 스프링 빈에 위임할 수 있습니다.

localhost:8080 루트로 요청을 보낼때, 혹은 다른 요청이라도 가장 첫 번째로 반응하는 것은 `DelegatingFilterProxy` 클래스입니다.

### SecurityFilterAutoConfiguration

SecurityFilterAutoConfiguration 에서 DelegatingFilterProxy 를 등록하는 것을 볼 수 있고, springSecurityFilterChain 이름으로 등록하는 것을 볼 수 있습니다.

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@EnableConfigurationProperties({SecurityProperties.class})
@ConditionalOnClass({AbstractSecurityWebApplicationInitializer.class, SessionCreationPolicy.class})
@AutoConfigureAfter({SecurityAutoConfiguration.class})
public class SecurityFilterAutoConfiguration {
    private static final String DEFAULT_FILTER_NAME = "springSecurityFilterChain";

    public SecurityFilterAutoConfiguration() {
    }

    @Bean
    @ConditionalOnBean(
        name = {"springSecurityFilterChain"}
    )
    public DelegatingFilterProxyRegistrationBean securityFilterChainRegistration(SecurityProperties securityProperties) {
        DelegatingFilterProxyRegistrationBean registration = new DelegatingFilterProxyRegistrationBean("springSecurityFilterChain", new ServletRegistrationBean[0]);
        registration.setOrder(securityProperties.getFilter().getOrder());
        registration.setDispatcherTypes(this.getDispatcherTypes(securityProperties));
        return registration;
    }

    private EnumSet<DispatcherType> getDispatcherTypes(SecurityProperties securityProperties) {
        return securityProperties.getFilter().getDispatcherTypes() == null ? null : (EnumSet)securityProperties.getFilter().getDispatcherTypes().stream().map((type) -> {
            return DispatcherType.valueOf(type.name());
        }).collect(Collectors.collectingAndThen(Collectors.toSet(), EnumSet::copyOf));
    }
}
```

- DelegatingFilterProxy

DelegatingFilterProxy 의 메서드 파라미터에 있는 targetBeanName 이 바로 springSecurityFilterChain 입니다.

```java
public DelegatingFilterProxy(String targetBeanName, @Nullable WebApplicationContext wac) {
    this.targetFilterLifecycle = false;
    this.delegateMonitor = new Object();
    Assert.hasText(targetBeanName, "Target Filter bean name must not be null or empty");
    this.setTargetBeanName(targetBeanName);
    this.webApplicationContext = wac;
    if (wac != null) {
        this.setEnvironment(wac.getEnvironment());
    }
}
```

### WebSecurityConfiguration

WebSecurityConfiguration 에서 springSecurityFilterChain 라는 이름으로 빈을 생성하는 것을 볼 수 있는데 this.webSecurity.build() 를 하면 FilterChainProxy 가 생성됩니다.

```java
@Bean(name = {"springSecurityFilterChain"})
public Filter springSecurityFilterChain() throws Exception {
    boolean hasConfigurers = this.webSecurityConfigurers != null && !this.webSecurityConfigurers.isEmpty();
    if (!hasConfigurers) {
        WebSecurityConfigurerAdapter adapter = (WebSecurityConfigurerAdapter)this.objectObjectPostProcessor.postProcess(new WebSecurityConfigurerAdapter() {
        });
        this.webSecurity.apply(adapter);
    }

    return (Filter)this.webSecurity.build();
}
```

## 참고

> [인프런. 스프링 부트 기반 스프링 시큐리티 프로젝트](#)
>
> [인프런. 스프링 시큐리티](#)
>
> [Spring Security Docs](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authentication-form)