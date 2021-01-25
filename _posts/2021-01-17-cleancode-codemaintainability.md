---
title: "코드 유지보수성(code maintainability)"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "클린 코드를 위한 코드 유지보수성(code maintainability)에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/CLEANCODE.jpg)

# 서론

이번 시간에는 코드 유지보수성(code maintainability)에 대해서 배워보겠습니다.

# 코드 유지보수성(code maintainability)

- `코드 유지보수성(code maintainability)`
    - 특정 기능을 담당하는 코드를 쉽게 찾을 수 있어야 한다.
    - 코드가 어떤 일을 수행하는지 쉽게 이해할 수 있어야 한다.
    - 새로운 기능을 쉽게 추가하거나 기존 기능을 쉽게 제거할 수 있어야 한다.
    - 캡슐화(encapsulation)가 잘 되어 있어야 한다. 즉 코드 사용자에게는 세부 구현 내용이 감춰져 있으므로 사용자가 쉽게 코드를 이해하고, 기능을 바꿀 수 있어야 한다.
    
만약에 코드의 유지보수성이 낮으면, 자신이 만든 코드가 몇개월, 몇년 뒤 다른 개발자가 비슷한 기능을 만드는 요구사항이 생길 때마다 `copy and write` 을 할 것이다. 이를 `안티-패턴(anti-pattern)`이라고 부른다.

- 한 개의 거대한 갓 클래스(god class) 때문에 코드를 이해하기 어렵다.
- 코드 중복(code duplication) 때문에 코드가 변화에 취약해진다.

## 참고

> [Real-world Software Development](#)