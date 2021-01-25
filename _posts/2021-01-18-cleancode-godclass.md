---
title: "갓 클래스(god class)와 코드 중복(code duplication)"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "클린 코드를 위한 갓 클래스(god class)와 코드 중복(code duplication)에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/CLEANCODE.jpg)

# 서론

이번 시간에는 갓 클래스(god class)와 코드 중복(code duplication)에 대해서 배워보겠습니다.

# 갓 클래스(god class)

- `갓 클래스(god class)`
    - 뚱뚱한 클래스, 하나의 거대한 클래스가 모든 일을 수행하는 경우
    - SRP 원칙을 지키지 못해서 클래스가 뚱뚱한 경우

# 코드 중복(code duplication)

- `코드 중복(code duplication)`
    - 여러 곳에 코드가 중복되어 있어서 기존 기능을 바꾸기 어렵게 한다.
    - 결국, 여러 곳에 걸쳐 수정이 필요하며 버그 발생률이 높아진다.

## 참고

> [Real-world Software Development](#)