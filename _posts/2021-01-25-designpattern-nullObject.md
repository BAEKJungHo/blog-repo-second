---
title: "널 객체(Null Object)"
layout: post
category: DesignPattern
tags: [DesignPattern]
excerpt: 널 객체(Null Object) 패턴 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/DesignPattern.jpg)

# 서론

널 객체(Null Object) 패턴에 대해서 배워보겠습니다.

# 널 객체(Null Object) 패턴

널 객체(Null Object) 패턴은 객체가 존재하지 않을 때 null 을 반환하는 대신에 필요한 인터페이스를 구현하는 객체(바디는 비어있음)를 반환하는 기법이다. 의도하지 않은 NullPointerException 과 null 확인 코드를 피할 수 있다.

이 패턴의 단점은, 데이터에 문제가 있어도 빈 객체를 이용해 실제 문제를 무시하기 때문에 나중에 문제를 해결하기 더 어려워질 수 있다.

## 예외 대안 기능

예외 대안 기능으로 `Optional<T>` 과 `Try<T>`도 있다.

- `Optional<T>`
    - 값이 없는 상태를 표현하는 내장 데이터 형식이다.
- `Try<T>`
    - 성공하거나 실패할 수 있는 연산을 가리키는 데이터 형식이다.
    - JDK 에서 자체적으로 지원하지 않기 때문에 외부 라이브러리를 사용해야한다.

## 참고

> [Real-world Software Development](#)