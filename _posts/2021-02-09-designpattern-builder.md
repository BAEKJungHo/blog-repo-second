---
title: "빌더(Builder)"
layout: post
category: DesignPattern
tags: [DesignPattern]
excerpt: "빌더(Builder) 패턴에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/DesignPattern.jpg)

# 서론

이번 시간에는 빌더(Builder) 패턴에 대해서 알아 보겠습니다.

# 플루언트 API

빌더 패턴을 배우기 전에 `플루언트 API`에 대해서 배워 보겠습니다.

플루언트 API 란 특정 문제를 더 직관적으로 해결할 수 있도록 특정 도메인에 맞춰진 API 를 가리킵니다.

플루언트 API 의 핵심은 `메서드 체이닝(method chaining)`입니다. 

- 플루언트 API
    - Stream API
    - Spring Integration
    - jOOQ

# 도메인 모델링

예를들어 `비지니스 규칙 엔진`을 만드는데 비지니스 사용자의 편의성을 개선하기 위해서 `어떤 조건이 주어졌을 때(when)`, `이런 작업을 한다(then)` 과 같은 간단한 조합을 규칙으로 사용할 수 있습니다.

해당 도메인에 아래와 같은 세가지 개념이 존재한다고 가정하겠습니다.

- 조건 : 어떤 팩트에 적용할 조건(true or false)
- 액션 : 실행할 연산이나 코드의 집합
- 규칙 : 조건과 액션을 합친 것. (조건이 참일 때 액션 실행)

코드에 도메인과 밀접한 이름(Condition, Action, Rule)을 사용하면 코드의 유지보수성이 증가합니다. 또한 도메인 언어를 코드에 적용하는 것은 `UBIQUITIOUS LANGUAGE` 패턴을 적용한 것이기 때문에 사용자와의 의사소통에도 도움을 줍니다.

# 빌더(Builder)

빌더 패턴의 코딩 스타일은 `메서드 체이닝(method chaining)`이 적용되어 다음과 비슷합니다.

```java
User user = User.build()
    .name("weave")
    .age(28)
    .build();
```

> 빌더 패턴에 대한 글은 [John Grib Blog](https://johngrib.github.io/wiki/builder-pattern/#%EC%9E%A5%EC%A0%90-2)를 참고하시면 많은 도움이 됩니다.

빌더 패턴은 단순하게 객체를 만드는 방법을 제공합니다. 생성자에 파라미터가 많은 경우 사용하기가 좋습니다. 빌더 패턴은 생성자의 파라미터를 분해해서 각각의 파라미터를 받는
여러 메서드로 분리합니다. 덕분에 각 메서드는 도메인이 다루는 문제와 비슷한 이름을 갖습니다.

예를들어 `when, then` 이라는 용어를 사용하고 싶은 경우 다음과 같이 할 수 있습니다.

> 아래 예제의 코드는 [Real-world Software Development](#)에서 발췌 했습니다.

```java
public class RuleBuilder {

    private Action action;

    public RuleBuilder when(final Condition condition) {
        this.condition = condition;
        return this;
    }

    public RuleBuilder then(final Action action) {
        this.action = action;
        return this;
    }

    public Rule createRule() {
        return new DefaultRule(condition, action);
    }

}
```

```java
Rule rule = new RuleBuilder()
    .when(facts -> "CEO".equals(facts.getFact("jobTitle")))
    .then(facts => {
        Mailer.sendMail(...);
    })
    .createRule();
```

해당 예제에서 when, then 등 `도메인 용어를 내장 생성자로 활용` 하지만 몇가지 문제가 아직 남아있습니다.

- RuleBuilder 인스턴스화
- createRule() 메서드 호출

따라서 다음과 같은 방법으로 코드를 개선할 수 있습니다.

- 사용자가 명시적으로 생성자를 호출하지 못하도록 생성자를 비공개로 설정한다.
- when 메서드를 정적 메서드로 만들어 이 메서드를 호출하면 예전 생성자를 호출하도록 한다. 게다가 정적 메서드를 제공하므로 Rule 객체를 설정하려면 어떤
메서드를 이용해야하는지 쉽게 알 수 있으므로 [발견성](https://baekjungho.github.io/designpattern-ubiquitiouslanguage/)도 개선된다.
- then 메서드가 객체의 최종 생성을 책임진다. 

```java
public class RuleBuilder {

    private Condition condition;

    private RuleBuilder(Condition condition) {
        this.condition = condition;
    }

    public static RuleBuilder when(Condition condition) {
        return new RuleBuilder(condition);
    }

    public Rule then(Action action) {
        return new Rule(condition, action);
    }

}
```

```java
final Rule ruleSendEmailToSalesWhenCEO =
        RuleBuilder
                .when(facts -> "CEO".equals(facts.getFact("jobTitle")))
                .then(facts -> {
                    var name = facts.getFact("name");
                    System.out.println("Relevant customer!!!: " + name);
                });
```

## 참고

> [Real-world Software Development](#)
>
> [John Grib Blog](https://johngrib.github.io/wiki/builder-pattern/)