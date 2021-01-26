---
title: "도메인 클래스(domain class)란 무엇인가?"
layout: post
category: OOP
tags: [OOP]
excerpt: 도메인 클래스(domain class)에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 도메인 클래스(domain class)

`도메인(domain)`이란 비지니스 문제와 동일한 단어와 용어를 사용한다는 의미이다.

`도메인 클래스(domain class)`란 예를들어 은행 입출금 내역을 분석하는 비지니스 문제를 프로그램으로 만들어야하는데,
입출금 내역이라는 비지니스 문제와 동일하게 클래스명으로 만들어 사용하는 것을 의미한다.

```java
class BankTransaction {
    private final LocalDate date;
    private final double amount;
    // 생략
}
```

## 참고

> [Real-world Software Development](#)