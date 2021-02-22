---
title: "Slf4j 와 Logback"
layout: post
category: Spring
tags: [Spring]
excerpt: "Slf4j 와 Logback에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

Slf4j 와 Logback 에 대해서 배워보겠습니다.

# 로깅 라이브러리

우리는 로그를 남기기 위해서 다양한 로깅 라이브러리 중에서 하나를 선택하여 사용할 수 있습니다.

- `java.util.logging`
    - JDK 1.4 부터 포함된 표준 로깅 API, 별도의 라이브러리를 추가할 필요가 없다.
    - 하지만 기능이 부족해서 잘 사용하지 않는다.
- `Apache Commons logging`
    - 아파치에서 제공하는 로깅 라이브러리
- `Log4j`
    - 아파치 재단에서 제공하며, 많이 사용되는 라이브러리
- `Logback`
    - Log4j 를 개발한 Ceki Gulcu 가 Log4j 의 단점을 개선하고 기능을 추가하여 개발한 로깅 라이브러리.
    - 최근에는 Slf4j 와 Logback 을 사용하는 추세이다.

> Slf4j 는 이러한 라이브러리들을 하나의 통일된 방식으로 사용할 수 있는 방법을 제공한다. Slf4j 는 `로깅 Facade(퍼사드)`이다. 즉, 로깅에 대한 추상 레이어를 제공하는 `interface 의 모음`이다.

## Slf4j

Slf4j API 를 이용하면 로깅 라이브러리가 어떤 것이든 같은 방법으로 로그를 남길 수 있다.

즉, 더 좋은 라이브러리가 생겨도 application 의 코드를 변경할 필요가 없다.

최근에는 `Slf4j 와 Logback`을 사용하는 조합이 대세이다.

# 참고

- [Slf4j 와 Logback 사용하기](https://enai.tistory.com/36)