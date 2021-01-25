---
title: "KISS(keep it short and simple) 원칙"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "클린 코드를 위한 KISS(keep it short and simple) 원칙에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/CLEANCODE.jpg)

# 서론

`KISS(keep it short and simple)` 원칙은 간단한 문제부터 시작하라는 의미로, 어떠한 요구사항들이 주어졌을 때 응용프로그램 코드를 한 개의 클래스로 구현하는 방식을
의미합니다.

예를들어 다음과 같은 요구사항이 있습니다.

매일 자정이 되면 영화관에서는 상영한 영화 이름, 시간, 가격, 사람 수 를 CSV 파일로 줍니다.

```
2020-01-11, abouttime, 09:00~11:30, 9900, 120
2020-01-11, avengers, 12:00~02:30, 12000, 100 
...
```

- 오늘 하루 상영한 영화에 대한 수익은 얼마인가 ?
- 1월달엔 어벤져를 본 사람이 몇명인가 ?
- 지출이 가장 높은 영화는 무엇인가 ?

요구사항이 위와 같을 때 `KISS` 원칙을 적용하여 "오늘 하루 상영한 영화에 대한 수익은 얼마인가 ?" 에 대한 요구사항을 하나의 클래스로 구현을 시작하는것입니다.

```java
public class MovieTransactionalAnalyzer {

    private static final String RESOURCES = "src/main/resources/";

    public static void main(final String... args) throws IOException {
        final Path path = Paths.get(RESOURCES + args[0]); // get csv file
        final List<String> lines = Files.readAllLines(path); // read all rows
        double total = 0d;
        for(final String line : lines) {
            final String[] columns = line.split(",");  // split comma
            final double amount = Double.parseDouble(columns[3]); // get amount
            total += amount; // accumulate amount
        }
        System.out.println("The total amount is " + total);

    }

}
```

처음에 신입으로 회사에 입사하여 흔히 신입들이 하는 안좋은 버릇 중 하나가, 머릿속으로 모든걸 설계 및 구현한 다음, 그것을 코드로 바로 옮기려고 한다는 것입니다.보통 간단한 문제는 상관없지만 여러 비지니스 로직을 거쳐서 개발해야하는 경우에는 `손으로 단계를 작성`하고 `KISS`원칙을 적용해서 간단한 문제부터 해결한 다음, `리팩터링`하는 방식을 추천합니다.

즉, 위 요구사항을 처음 받게되면 머릿속으로 한번 생각해보고, 생각이 잘 안나거나 중간에 다른 생각으로 빠진다 그러면 다음과 같은 단계를 거쳐서 연습하는 방식을 추천합니다.

1. 손으로 구현해야하는 비지니스 로직 단계를 작성
2. KISS 원칙으로 간단한 문제부터 해결
3. 리팩터링

이 방식은 나중에 복잡한 응용프로그램을 구현할 때에도 큰 도움이 되는 절차입니다.

## 참고

> [Real-world Software Development](#)