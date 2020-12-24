---
title: "RequestMappingHandlerMapping"
layout: post
category: Spring
tags: [Spring]
excerpt: "RequestMappingHandlerMapping 을 통한 RequestMapping 전략"
author: BAEKJungHo
---

* content
{:toc}

# 서론

RequestMappingHandlerMapping  에 대해서 알아봅시다.

# RequestMappingHandlerMapping 

스프링은 `@RequestMapping` 어노테이션을 이용하여 요청을 처리할 컨트롤러를 구하는데 그 역할을 담당하는 클래스가 스프링 3.1 버전 이전에는 `DefaultAnnotationHandlerMapping` 이
기본 HandlerMapping 이었는데 3.1 버전 이후로는 deprecated 되어서 `RequestMappingHandlerMapping` 이 기본 HandlerMapping 이 되었습니다.

```java
public class RequestMappingHandlerMapping extends RequestMappingInfoHandlerMapping
		implements MatchableHandlerMapping, EmbeddedValueResolverAware {

	private boolean useSuffixPatternMatch = true;

	private boolean useRegisteredSuffixPatternMatch = false;

	private boolean useTrailingSlashMatch = true;

	private Map<String, Predicate<Class<?>>> pathPrefixes = new LinkedHashMap<>();

	private ContentNegotiationManager contentNegotiationManager = new ContentNegotiationManager();

    // 생략

}
```

## @RequestMapping 과 템플릿 변수

@RequestMapping("/{siteCode}") 이런식으로 템플릿 변수를 사용해서 URL 이 넘어오는 경우 템플릿 변수인 siteCode 에 `/test.html` 이런식으로 입력했을때 실제로 저 템플릿 변수에 들어오는 값은 `test`입니다.

스프링의 `@ReqeustMapping` 기본 전략은 RequestMappingHandlerMapping 이 안에서 볼 수 있는데 `useSuffixPatternMatch` 기본값이 true 입니다.

따라서 `.(마침표)` 를 자르지 않고 템플릿 변수에 바인딩 시키기 위해서는 스프링 부트에서 `WebMvcConfigurerAdapter` 를 상속 받은 `WebConfig` 클래스에서 설정이 필요합니다.

```java
@Override
public void configurePathMatch(PathMatchConfigurer configurer) {
    configurer.setUseSuffixPatternMatch(false);
}
```   
