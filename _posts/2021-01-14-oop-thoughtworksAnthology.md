---
title: "객체지향 생활체조 9가지 규칙"
layout: post
category: OOP
tags: [OOP]
excerpt: "소트웍스 앤솔러지(Thoughtworks Anthology) 책에 나와있는 객체지향 생활체조 9가지 규칙에 대해 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

소트웍스 앤솔러지(Thoughtworks Anthology) 책에 나와있는 객체지향 생활체조 9가지 규칙에 대해 정리한 글입니다.

TDD, OOP, 리팩터링 연습을 위해서 사용되는 좋은 예제들이 있습니다.

- 로또(단, UI는 콘솔)
- 사다리 타기(단, UI는 콘솔)
- 볼링 게임 점수판(단, UI는 콘솔)
- 체스 게임(단, UI는 콘솔)
- 지뢰 찾기 게임(단, UI는 콘솔)

OOP 연습 시, 객체지향 생활체조 9가지 규칙을 지키려고하는것도 좋은 방법 중 하나일 것 같습니다.

# 객체지향 생활 체조

- 훈련 규칙
    - 한 메서드에 오직 한 단계의 들여쓰기만 한다.
    - else 예약어를 쓰지 않는다.
    - 모든 원시값과 문자열을 포장(wrap)한다.
    - 한 줄에 점을 하나만 찍는다.
    - 줄여쓰지 않는다.
    - 모든 엔티티(entity)를 작게 유지한다.
    - 2개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
    - first-class 컬렉션을 사용한다.
    - getter/setter property 를 쓰지 않는다.

## 규칙 1. 메서드당 들여쓰기 한번

메서드당 들여쓰기 한번이라는 것은 for 문이나 if 문 등 들여쓰기 단계가 2단계 이상되어있으면 따로 메서드로 빼내어
들여쓰기 단계를 1단계로 리팩터링 하라는 것입니다.

한가지 예시를 들어 설명하겠습니다.

간단하게 미국을 여행하려고하는데 본인이 가지고 있는 금액을 판단하여 최적의 항공사를 매칭시켜주는 프로그램이 있다고 해보겠습니다.

금액이 500 만원 이상 있으면 아시아나 항공, 200만원 이상 있으면 대한 항공, 100만원 이상 있으면 티웨이 항공을 매칭한다고 해보겠습니다.

```java
public class AirlineFactory {

    public static Airline createAirline(User user) {
        if(user.canTravel()) {
            if(user.getMoney() >= 5000000) {
                return new AsianaAirline();
            } 
            if(user.getMoney() >= 2000000) {
                return new DaehanAirline();
            }
            if(user.getMoney() >= 1000000) {
                return new TwayAirline();
            }
        }
    }

}
```

들여쓰기 단계가 지금 2 단계 이지만, 들여쓰기 단계가 많아질 수록 코드의 양도 많아지고 가독성도 떨어질 것입니다. 그리고 위에서 금액을 하드코딩으로 되어있는데, 하드코딩은 클린 코드를 만들기 위해서 피해야할 안티패턴 중 하나입니다.

위 문제를 Enum 을 사용하여 들여쓰기 단계를 1단계로 만들고 하드코딩도 없애보겠습니다.

```java
@Getter @Setter
public class User {

    private int money;

    public boolean canNotTravel() {
        return this.money == 0;
    }

}
```

canTravel() 에서 canNotTravle() 로 메서드를 변경했습니다. 조건문 앞에 `!(느낌표)` 를 붙여 부정을 나타내는 것보다는 그에 알맞은 네이밍을 하여 메서드로 만드는 것이 가독성에도 더 좋습니다.

```java
@Getter
@AllArgsConstructor
public enum AirlineMatcher {

    ASIANA(new AsianaAirline(), 5000000),
    DAEHAN(new DaehanAirline(), 2000000),
    TWAY(new TwayAirline(), 1000000);

    private Airline airline;
    private int money;

    public static Airline match(User user) {
        for (AirlineMatcher matcher : AirlineMatcher.values()) {
            if (user.getMoney() >= matcher.getMoney()) {
                return matcher.getAirline();
            }
        }
        throw new RuntimeException("금액이 부족하여 항공사 매칭에 실패하였습니다.");
    }

}
```

각 항공사 이름을 따서 enum 으로 만들었고, 속성으로 구현체와 금액을 가지고 있습니다.

```java
public class AirlineFactory {

    public static Airline createAirline(User user) {
        if(user.canNotTravel()) {
            throw new RuntimeException("잔고가 0원 입니다.");
        }
        return AirlineMatcher.match(user);
    }

}
```

팩토리 메서드 패턴을 이용해서 항공사를 찾습니다.


```java
public interface Airline {

    void goAmerica();

}

public class AsianaAirline implements Airline {

    @Override
    public void goAmerica() {
        System.out.println("아시아나 비행입니다. 미국까지 안전하게 모시겠습니다.");
    }

}

public class DaehanAirline implements Airline {

    @Override
    public void goAmerica() {
        System.out.println("대한항공 비행입니다. 미국까지 안전하게 모시겠습니다.");
    }

}

public class TwayAirline implements Airline {

    @Override
    public void goAmerica() {
        System.out.println("T way 비행입니다. 미국까지 안전하게 모시겠습니다.");
    }

}
```

```java
public class AirlineApplication {

    public static void main(String[] args) {
        User user = new User();
        user.setMoney(5000000);
        Airline airline = AirlineFactory.createAirline(user);
        airline.goAmerica();
    }

}
```

이렇게 들여쓰기 1번을 하게되면 메서드 자체의 `수직 scope` 도 줄어들고 `버그 판별`, `유지보수성`, `재사용성` 측면에서 좋아집니다.

## 규칙 2. else 예약어 금지

조건문은 `복제의 원흉`이기도 합니다.

```java
public static void drive() {
  if(isReady()) {
     System.out.println("운전 시작");
  } else {
    // other code
  }
}
```

```java
public static void endMe() {
  if(isReady) {
    System.out.println("운전 시작");
    return;
  } 
  // other code
}
```

또는 삼항 연산자를 이용해서 1줄로 간단하게 나타낼 수도 있습니다. return 문을 일찍 쓰는 것을 너무 많이 하면 간결함을 저해하기 쉽다는 점을 간과해서는 안됩니다.

특히 길이가 긴 함수에서 else 절은 로직 파악을 어렵게 해 코드 탐구를 처음부터 다시 시작하게 하는 주요인이 됩니다.

> 만약에 상태에 대한 분기가 몇 군데 걸쳐 중복되어 있을 때 `전략 패턴(Strategy Pattern)`은 유용합니다.

## 규칙 3. 원시값과 문자열의 포장

int 값 하나 자체는 아무 의미 없는 스칼라 값일 뿐입니다. 어떤 메서드가 int 값을 매개변수로 받는다면 그 메서드 이름은 해당 `매개변수의 의도를 나타내기위해 수단과 방법을 가리면 안됩니다.` 

원시형 변수로는 컴파일러가 의미적으로 맞는 프로그램 작성을 안내할 수 없다. 오브젝트라면 아주 사소하더라도 컴파일러와 프로그래머에게 어떤 값이며 왜 쓰이고 있는지에 대한 정보를 전하는 셈입니다.

## 규칙 4. 한 줄에 한 점만 사용

한 줄에 한 점이상 있다는 것은 하나 이상의 동작이 이루어지고 있다는 것입니다.

중복된 점들은 캡슐화를 어기고 있다는 방증이고 오브젝트가 자기 속을 들여다 보려 하기보다는 뭔가 작업을 하도록 만들려고 해야 합니다.

이 규칙을 지키기 위한 좋은 출발 점이 있는데 바로 `디미터 법칙(Law of Demeter)` 입니다.

> 디미터 법칙(Law of Demeter, 친구하고만 대화하라)는 이 규칙의 좋은 출발 점이다.

## 규칙 5. 축약 금지

축약 금지는 [클린 코드를 위한 좋은 네이밍 방법](https://baekjungho.github.io/cleancode-naming/#%EC%B6%95%EC%95%BD%EC%96%B4%EB%8A%94-%EA%B8%88%EC%A7%80)에 나와있는 내용과 비슷합니다.

클래스명이 Car 인 경우 메서드명을 driveCar 라고 지을 필요가 없습니다. 클래스명으로 유추가 가능하기 때문에 car.drive() 이런식으로 만들어서 호출하는 편이 좋습니다.

## 규칙 6. 모든 엔티티를 작게 유지

클래스 안의 코드나, 패키지당 클래스 개수를 작게 유지하라는 규칙입니다. 보통 클래스는 50줄 메서드는 10줄 ~ 15줄 정도로 유지하는게 좋다고 합니다. 

## 규칙 7. 2개 이상의 인스턴스 변수를 가진 클래스 사용 금지

많은 인스턴스 변수를 지닌 클래스를 대상으로 응집력 있는 단일 작업을 설명할 수 있는 경우는 거의 없습니다.

## 규칙 8. 일급 컬렉션 사용

컬렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다는 규칙입니다.

컬렉션이 기본으로 제공하는 add, remove, iterate 와 같은 기본 동작 외에 validation, filtering 등 추가 로직이 필요한 경우 일급 컬렉션을 고려해볼 것을 추천합니다.

## 규칙 9. 게터/세터/속성 사용 금지

## 정리

소트웍스 앤솔러지에서 제시하는 "객체지향 생활체조 9원칙"에 대해서 정리해보았습니다.

하지만 실제로 복잡한 웹 애플리케이션을 개발하다보면 한 클래스에 2개 이상의 인스턴스 변수를 가지고 있어야 하는 경우도 많고 getter setter 의 사용도 상당히 많습니다. 

무작정 객체지향 생활체조 전부를 따라한다기 보다는 OOP 연습과 리팩터링 연습을 위해서 최대한 지키려고 노력해보는 과정에서 `어디가 개선해야할 코드인지 빠르게 찾아 낼 수 있으며, 좋은 코드 작성을 위한 생각과 노력을 더 기울이게 될거라고 생각합니다.`

## 참고

> [효과적으로 TDD, 리팩터링, OOP 를 연습하는 방법은 ?](https://medium.com/@codesquad_yoda/%ED%9A%A8%EA%B3%BC%EC%A0%81%EC%9C%BC%EB%A1%9C-tdd-%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81-oop%EB%A5%BC-%EC%97%B0%EC%8A%B5%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95%EC%9D%80-7ecc9ddb5d45)
>
> [인프런. 당신을 함께 일하고 싶은 개발자로 만들어 줄 클린 코드 작성법](#)
>
> [The Toughtworks Anthology](#)