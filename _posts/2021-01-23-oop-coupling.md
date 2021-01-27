---
title: "결합도(coupling)"
layout: post
category: OOP
tags: [OOP]
excerpt: 결합도(coupling)에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

응집도와 마찬가지로 소프트웨어 설계에 있어서 고려해야할 중요한 사항인 결합도에 대해서 배워보겠습니다.

# 결합도(coupling)

응집도와 마찬가지로 결합도도 코드를 구현할 때 고려해야 할 중요한 특성이다. 응집도는 패키지, 클래스, 메서드 등이 얼마나 서로 관련되어있는지를
나타내는 반면에, 결합도는 `한 기능이 다른 클래스에 얼마나 의존하고 있는지`를 나타낸다. 즉, 하나의 클래스에서 얼마나 많은 클래스를 참조하고 있는가로 설명할 수 있다.

즉, 결합도는 `의존성(dependency)`과 연관이 있다. 결합도는 낮을 수록 좋은데, 결합도를 낮추는 좋은 방법중 하나가 `인터페이스(interface)`를 사용하는 것이다. 인터페이스를 사용하면 요구사항이 바뀌더라도 유연성을 유지할 수 있다.

[인터페이스를 두어서 얻는 이점](https://baekjungho.github.io/oop-polymorphism/#%EB%8B%A4%ED%98%95%EC%84%B1%EA%B3%BC-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)은 세부 구현체를 숨기고 인터페이스를 바라보게 함으로써 클래스 간의 의존관계를 줄이는 것, 결합도를 낮추고, 다형성을 위해서이다.

예를들어 영화 내역을 파싱하는 프로그램을 만들어야 한다고 할때 클래스(구현체)에 의존시키는것이아니라, 파싱이라는 역할을 담당하는 인터페이스만들어서 사용한다.

- 인터페이스

```java
public interface MovieScreenParser {
    Movie parseFrom(String line);
    List<Movie> parseLinesFrom(List<String> lines);
}
```

- 구현체

```java
public class MovieScreenCSVParser implements MovieScreenParser {
    // ...
}

public class MovieScreenJSONParser implements MovieScreenParser {
    // ...
}
```

이렇게 인터페이스를 두면 다형성의 특징을 살릴 수 있기 때문에 결합도가 자연스레 낮아진다.

## 참고

> [Real-world Software Development](#)