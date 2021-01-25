---
title: "중복 배제 원칙(Don't repeat yourself, DRY)"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "클린 코드를 위한 중복 배제 원칙(Don't repeat yourself, DRY)에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/CLEANCODE.jpg)

# 서론

이번 시간에는 중복 배제 원칙(Don't repeat yourself, DRY)에 대해서 배워보겠습니다.

# 중복 배제 원칙(Don't repeat yourself, DRY)

`중복 배제 원칙(Don't repeat yourself, DRY)` 은 반복을 제거하면 로직을 바꿔도 여러 곳의 코드를 바꿔야 할 필요성이 없어진다.

중복을 제거하는 이유는 `코드 유지보수성`과 `재사용성`, `확장성`이다. 

예를들어 스프링을 사용할 때 비지니스 로직을 service layer 에 구현하는 이유가 바로 재사용성과, 확장성 때문이다. 만약 비지니스 로직이 각 컨트롤러의 핸들러 메서드에 구현되어있다면, 다른 서비스에서 해당 로직을 재사용하고 싶어도 `copy and write` 하거나 `리팩토링`을 해야할 것이다.

## 참고

> [Real-world Software Development](#)