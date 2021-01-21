---
title: "클린 코드를 위한 좋은 함수 작성 방법"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "클린 코드를 위한 좋은 네이밍 방법에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/CLEANCODE.jpg)

# 서론

이전 시간에는 [클린 코드를 위한 좋은 네이밍 방법](https://baekjungho.github.io/cleancode-naming/)에 대해서 배웠습니다.

이번에는 좋은 함수를 작성하는 방법에 대해서 배워보겠습니다.

해당 내용의 출처는 [인프런. 당신을 함께 일하고 싶은 개발자로 만들어 줄 클린 코드 작성법](https://www.inflearn.com/course/%ED%81%B4%EB%A6%B0%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%EB%B2%95/dashboard)이며 저렴한 가격에 더 다양한 내용을 배우실 수 있습니다.

# 좋은 함수 작성 방법

요즘은 `함수를 작게 만드는 것이 추세` 이다. 왜 짧은 길이의 함수가 추세가 되었을까 ? 함수는 작을수록 가독성와 유지보수에 좋으며 버그 발생 가능성도 적기 때문이다.

전체 코드가 충분히 의도가 분명한 좋은 이름의 작은 함수들로 구성되면, 코드 파악 시 관심 있는 작은 단위의 함수 하나만 살피면 된다. 반면 함수 길이가 긴 경우에는
긴 함수 전체를 탐구하느라 정말 많은 시간이 낭비된다.

> 작은 함수가 좋다고 큰 함수를 작은 덩어리들로 대충 나누라는 말이 아니다. 함수를 여러 개의 작은 함수로 나눌 때는 최대한 의미 있게 분리해야 한다. 
잘못하다 논리가 쪼개지면 관심 있는 작은 단위의 함수 하나만 살피기가 어려워진다.

그러나 함수가 짧아진다는 것은 함수의 수가 많아지는 만큼 전체 코드를 알아보는 것이 더 어려워 질 수도 있다. 함수에서 함수로 코드를 다시 찾아가는 과정에서
스크롤이 더 자주 발생하기 때문이다.

실제로 위와 같은 이유로 함수를 너무 작게 분해하는 것을 싫어하는 사람들도 있다. 클래스가 담당하는 책임이 많을 수록 분리해야 할 함수의 수도 그만큼 많아진다.

> 소프트웨어 개선 그룹(SIG)의 유지 보수성 판정 통계에 따르면 유지 보수성이 훌륭한 프로그램에는 공통으로 길이가 15줄을 넘어가지 않은 함수가 대부분을 차지했다고 한다.

유사하게 클래스도 작아야한다. 즉 가능한 클래스가 갖는 책임을 줄야 한다.

## 개행 할 것

스크롤이 적을 수록 코드를 이해하기 좋다고 말했다. 코드 길이가 매우 긴 함수나 호출자 함수로부터 너무 멀리 떨어져 있는 내부 함수를 탐험하기 위해
스크롤이 발생하면 확실히 코드 이해가 오래 걸리고 힘들어진다.

같은 이유로 `수평 방향 스크롤`도 고려해야 한다. 따라서 메서드의 파라미터가 많은 경우 아래와 같이 하는 방법도 수평 방향 스크롤을 고려한 부분 중 하나이다.

```java
public findAllEventJoinUser(
  AAA aaa,
  BBB bbb,
  CCC ccc,
  DDD ddd
) {

}
```

## 하나만 하라

함수는 하나의 일만 해야 한다는 말을 많이 들어봤을 것이다. 이 말의 진정한 의미는 아래와 같다.

http get 요청을 보내는 get() 함수가 있다고 가정한다. 이 함수는 내부적으로 

- (1) tcp 소켓을 생성해 
- (2) 서버와 connection 을 맺고 
- (3) get 요청 메시지를 생성해
- (4) 요청 메시지를 서버에 전송한다.

그러나 이 함수는 하나의 역할을 수행한다고 볼 수 있다.

그 이유는 get() 이라는 `함수 이름이 갖는 추상화 수준` 에서는 `http get 요청을 한다.` 는 단 한 가지 역할만 수행하기 때문이다.

예를 들어 validateAndReport() 라는 함수가 있다고 하자. 이름대로 이 함수는 어떠한 상태에 대해 검증하고 그 결과를 알린다. 두 가지 일을 하는것 같지만, 이 함수 이름의 추상화 수준에서는 한 가지 일을 하는 것이 맞다. 만약 validate 와 report 외에 다른 일까지 수행하다면 단일 행동을 한다고 볼 수 없다.

> 즉, 함수가 하나의 일만 해야 한다는 것은 `함수 이름이 갖는 추상화` 수준을 기준으로 판별해야 한다.

## 실용성 vs 단순성

함수 사용에 편의를 제공할 것이냐 아니면 함수 코드를 단순하게 가져갈 것이냐는 상황에 따라 결정하기 쉽지 않을 수도 있다. 그러나 개인적으로는 코드의 단순성을 더 중요한 가치로 생각한다. 만약 이 두가지 가치 사이에서 고민이 된다면 우선 최대한 입력에 엄격한 함수를 작성해야 한다.

처음부터 입력에 엄격한 함수를 나중에 다양한 입력을 받도록 확장하는 것은 쉽다. 반면 처음부터 다양한 입력을 받는 함수는 후에 입력에 엄격한 함수로 바꾸려면 큰 비용이 든다.

> 입력에 유연한 함수가 만약 공개 API라면 하위 호환성 문제로 함수를 변경할 수 없게 될 것이다.

## 일관된 추상화 수준

함수 내부의 추상화 수준에 대해서 알아보자.

```java
void validateAndReport() {
  // validate
  validate();
  
  // report
  Reporter reporter = new Reporter();
  reporter.setX();
  reporter.setY();
  reporter.report();
}
```

위 validateAndReport() 함수는 추상화 수준이 일관되지 않는다. report 라는 추상화 수준은 없고 report 보다 한 단계 아래 수준의 코드가 있다.

위 코드는 아래와 같이 추상화 수준을 일관되게 맞춰 줘야 한다.

```java
void validateAndReport() {
  validate();
  report();
}
```

## 중괄호 중첩

함수를 작성할 때 중괄호 사용은 최대한 피해야 한다. 특히 `중첩된 중괄호는 코드의 가독성을 현저하게 저하시킨다.` 참고로 함수의 길이가 길면 길수록 함수 내의 중괄호가 중첩되는 케이스가 많다.

- `only one level of indentation per method`

소트웍스 앤솔러지의 "객체지향 생활 체조" 에서는 메서드 당 들여쓰기 깊이가 한 단계가 넘지 말아야 한다고 한다.

```java
class BadukBoard {
  public void showBoard() {
    for(int i=0; i<VERTICLA_SIZE; i++) {
      for(int k=0; k<HORIZONTAL_SIZE; k++) {
        showCell(i, j);
      }
    }
  }
}
```

위와 같이 두 단계 이상 들여쓰기로 중첩을 이루면 가독성이 저해된다. 아래와 같이 바꿀 수 있는지 고민해봐야 한다.

```java
class BadukBoard {
  public void showBoard() {
    for(int i=0; i<VERTICLA_SIZE; i++) {
      showLine(i);
    }
  }
}
```

## 매개변수 개수

함수의 매개변수 개수는 적을수록 좋다. 매개변수는 많을 수록 가독성을 크게 저하시킨다. 단점은 가독성 뿐만 아니라 같은 타입의 매개변수가 연속으로 선언된 경우
실수가 발생할 가능성이 커져 예상치 못한 버그를 일으킬 수 있다.

## 플래그 인수

가끔 boolean 타입의 인자를 받아 if 절 등으로 로직을 분기하는 함수를 볼 때가 있다. 이런 함수는 전형적인 책임이 많은, 여러 가지를 하는 함수이다.

> 인자가 true 일 땐 이런 동작을, false 일 땐 이런 동작을

좋은 방식이 아니다. 함수 안에서 `분기가 나뉘는 만큼 메서드가 어떻게 동작할지를 파악하는데 어려움`을 주기 때문이다. 플래그 인수는 사용하지 말고
각각의 경우에 해당하는 함수를 따로 정의하는게 좋다.

## 인수 클래스

보통 매개변수 개수가 많은 함수를 리팩토링하기 위해 인수 목록 대신 클래스를 정의한다. 각각의 매개변수를 그 클래스의 프로퍼티로 두는 것이다. 맵에 담아 전달하는 사람도 있다.

함수에 매개변수 개수가 많아지면 이런 방식을 사용하되, 최대한 매개변수 개수가 많아지지 않게 할 방법이 있는지부터 고민해야 한다.

## 인자 유효성 검사

간단하게는 null 검사부터 비지니스에 특화된 복잡한 검증까지, 안정성 있는 프로그램을 작성하려면 함수 인자에 대한 유효성 검사를 철저하게 해야 한다.

기초적인 만큼 기본적으로 반드시 지켜야 하는 원칙이다. 그러나 불필요한 검사나 중복 검사는 피해야 한다. 내부 함수로 전달될 인자가 유효성을 확실히 만족하는 경우에는 굳이 다시 한 번 검사할 필요는 없다.

> 클린 코드에서는 가능하면 함수 인자로 null 을 전달하지 말라고 한다. 호출되는 함수에 불필요한 검사 코드가 생기기 때문이다.

위에서 내부 함수로 전달된 인자가 유효성을 만족함이 확실히 보장된 경우에는 추가 검사가 필요 없다고 말씀드렸습니다. 이런 내부 함수의 코드를 본 동료 개발자는 다음과 같이 생각할 것입니다.

"이 함수의 인자는 유효성을 확실히 만족하나보구나!" (유효성 검사같이 기본적인 걸 빼먹을 리 없지.)

이 지점이 문제이다. 만약 부주의로 유효성 검사를 빼먹은 경우, 이 실수를 아무도 알아차리지 못할 수 있다. 

너무 당연하고 기본적인 원칙이라 이런 일이 발생할 수 있다.

## 방어적 복사본 반환

보통 예상 못 한 실수로 객체의 상태가 변경되는 것을 막고자 불변 객체를 자주 사용한다.
같은 이유로 객체 내부의 상태를 반환할 때는 방어적 복사본을 반환할 필요가 있을지 고민해보는 것이 좋다. 
특히 공개 라이브러리 클래스의 변경 가능한 내부 상태를 반환할 때는 반드시 방어적 복사본을 반환해야 한다.

```java
public class NonEmptyStringsHolder {
    private final List<String> nonEmptyStrings = new ArrayList<>();

    public List<String> getNonEmptyStrings() {
        return nonEmptyStrings;
    }

    public void addNonEmptyString(String nonEmptyString) {
        if(StringUtils.isEmpty(nonEmptyString))
            throw new IllegalArgumentException("Argument is empty");
        nonEmptyStrings.add(nonEmptyString);
    }

    public static void main(String[] args){
        NonEmptyStringsHolder nonEmptyStringsHolder = new NonEmptyStringsHolder();
        nonEmptyStringsHolder.addNonEmptyString("empty");
        nonEmptyStringsHolder.addNonEmptyString("blank");
        
        List<String> nonEmptyStrings = nonEmptyStringsHolder.getNonEmptyStrings();
        nonEmptyStrings.add("");
        
        System.out.println(nonEmptyStringsHolder.getNonEmptyStrings());
    }
}
```

NonEmptyStringsHolder 클래스의 addNonEmptyString() 메서드가 인자 유효성 검사를 잘 했음에도 불구하고 콘솔 출력 결과는 다음과 같다.

```
[empty, blank, ]
```

NonEmptyStringsHolder 클래스의 getNonEmptyStrings() 메서드가 다음과 같이 복사본을 반환한다면

```java
 public List<String> getNonEmptyStrings() {
   return new ArrayList<>(nonEmptyStrings);
 }
 ```
 
 아래 결과와 같이 NonEmptyStringsHolder 클래스의 내부 상태는 변경되지 않는다.
 
 ```
 [empty, blank]
 ```
 
> 유사한 원리로 클라이언트가 생성자 등에 전달한 객체를 내부 상태로 설정할 때도 방어적 복사를 고려해봐야 한다.

## 함수 배치

코드는 위에서 아래로 자연스럽게 읽혀야 한다. 한 함수가 있다면 아래에는 그 함수로부터 호출된 순서로 한 단계 추상화 수준이 낮은 내부 함수들을
위치시키는 것이 적절한 함수 배치 방법이다. (신문 기사 배치 방식) 그러나 이 원칙에도 유연성이 필요하다.

`한 클래스 내의 두 개의 함수에서 공통으로 호출하는 내부 함수는 어디에 위치시키는 것이 좋을까?`

개인 적으로는 두 함수의 아래에 두는게 좋다고 생각한다. 위와 같은 내부 함수는 주로 private 으로 선언되는데 public 함수들을 위로 올리고
private 함수들은 아래에 모아두는 방식을 취하는게 좋다.

## 공개 함수의 내부 구현

앞에서 퍼블릭 함수의 이름에는 구현 디테일(how)이 들어가지 않는 것이 좋다고 말했다. 함수를 호출하는 코드가 구현 디테일을 염두하여 작성될 가능성이 커지기 때문이다.

마찬가지로 호출하는 퍼블릭 함수의 구현 디테일을 염두하여 코드를 작성하면 호출자 코드는 구현 디테일의 변경에 취약해진다.

성능 이슈나 그 외의 더 복잡한 문제와 관련된 예외가 있기는 하다.

## 이름 길이와 스코프

`변수`의 유효 범위가 넓을 수록 긴 길이의 이름이 짧은 길이의 이름보다 좋다. 변수의 의미에 관한 혼란을 줄여주기 때문이다.

`함수`의 스코프에 관련된 이름의 길이는 스코프가 넓은 함수일 수록(자주 호출되는 함수 이름일 수록) 이름이 짧을 수록 좋다. 그러나 필요한 의미를 잃어도 된다는 말은 아니다.
특히 함수가 `public` 이라면 이름은 가능한 짧아야 한다. 다시 말해 `내부 구현을 이름에 담지 말아야 한다는 것이다.` 반대로 내부 함수는 이름이 길수록 좋다.
내부 함수는 api의 일부가 아니므로 구체적인 동작을 이름에 충분히 담아 서술성을 만족해줘야 한다. 긴 이름의 내부 함수는 그 함수의 동작에 대한 `documentation` 역할을 하기 때문이다.

`클래스`의 이름도 자주 호출될 수록 짧은 이름이 좋다.

그러나 예외가 있는데 하위 클래스가 많은 경우이다. 하위 클래스가 많을수록 각각의 클래스가 확실히 구분될 수 있도록 이름이 충분히 서술적이어야 한다.

> "The length of a variable name should be proportional to its scope. The length of a function or class name is the inverse." - Uncle Bob Martin