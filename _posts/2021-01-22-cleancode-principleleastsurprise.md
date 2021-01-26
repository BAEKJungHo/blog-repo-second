---
title: "놀람 최소화 원칙(principle of least surprise) 이란?"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "놀람 최소화 원칙(principle of least surprise) 이란?"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/CLEANCODE.jpg)

# 서론

이번 시간에는 놀람 최소화 원칙(principle of least surprise)에 대해서 알아 보겠습니다.

# 놀람 최소화 원칙(principle of least surprise)

메서드를 구현할 때에는 `놀람 최소화 원칙(principle of least surprise)` 을 적용해야 한다.

놀람 최소화 원칙(principle of least surprise)이란 어떤 메서드가 예상치 못한 방법으로 동작한다면 코드를 이해하기 어려울 것이다. 따라서 일관성을 유지하는 범위에서 코드를 구현할 것을 강조하는 원칙이다.

만약에 메서드명이 drive() 인데 실제로 동작하는 것은 break() 이면 안된다. 즉, 메서드 이름은 해당 메서드가 어떠한 동작을 하는지 알 수 있어야하며 이름대로 동작해야한다. 또한 함수의 이름과 같은 [일관된 추상화 수준](https://baekjungho.github.io/cleancode-goodFunction/#%EC%9D%BC%EA%B4%80%EB%90%9C-%EC%B6%94%EC%83%81%ED%99%94-%EC%88%98%EC%A4%80)을 제공해야 한다.

## 참고

> [Real-world Software Development](#)