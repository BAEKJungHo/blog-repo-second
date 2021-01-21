---
title: "[객체지향 3대 요소] 캡슐화(Encapsulation)"
layout: post
category: OOP
tags: [OOP]
excerpt: "객체지향 3대 요소인 캡슐화에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

객체지향 3대 요소인 캡슐화(Encapsulation)에 대해서 배워 보겠습니다.

# 캡슐화(Encapsulation)

캡슐화란 내부 `정보를 감추고` 외부의 변경에 `유연`하게 대처하기 위한 객체지향 요소입니다.

> 캡슐화(정보 은닉)의 기본 원칙은 `모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다`는 것입니다.

"정보를 감춘다 ?" 

어떤 정보를 감춘다는 의미일까요 ?

바로 외부에서 클래스 내부에 함부로 접근하여 값을 변경하지 못하게 `클래스 내부의 속성이나 메서드`를 감춘다는것을 의미합니다.

- 캡슐화를 사용하지 않은 경우

```java
public class User {

  public String name; 
  public String id;
  public String age;

  public String getName() {
    return this.name;
  }
  
  public String getId() {
    return this.id;
  }
  
  public String getAge() {
    return this.age;
  }

}
```

외부에서 다음과 같이 Administrator 클래스에 있는 속성 값을 변경할 수 있습니다.

```java
@Service
public class UserService {

    public void join(User user) {
        user.name = "BAEKJH"; // 변경에 취약
        // 생략
    }

}
```

public 클래스의 필드는 가급적 public 이어서는 안됩니다. 필드가 수정될때 락 획득같은 작업을 할수 없고, 스레드로부터 안전하지 않습니다.

- 캡슐화를 사용한 경우

```java
public class User {

  private String name; 
  private String id;
  private String age;

  public String getName() {
    return this.name;
  }
  
  public String getId() {
    return this.id;
  }
  
  public String getAge() {
    return this.age;
  }
  
  public void setName(String name) {
    // 이름에 대한 검증 추가
    if(name.length() > 5) {
        throw new RuntimeException("이름은 5글자를 초과할 수 없습니다.");
    }
    this.name = name;
  }
  
  public void setId(String id) {
    this.id = id;
  }
  
  public void setAge(String age) {
    // 나이에 대한 검증 추가
    if(age.length() > 3) {
        throw new RuntimeException("나이는 3자리를 초과할 수 없습니다.");
    }
    this.age = age;
  }

}
```

접근 지시자(Access modifier)를 private 선언하고 public 인 `getter/setter` 메서드를 사용해서 캡슐화를 하였습니다. 외부에서 User 의 속성에 직접 접근해서 값을 변경할 수 없게되었습니다.

이렇게 캡슐화를 하면 외부에서 접근할 수 있는 대상이 제한되기 때문에 다른 코드들과의 `결합도가 낮아`집니다.

결합도는 낮을 수록 좋은데(decoupling) 결합도가 낮다는건 클래스간의 의존관계가 낮다는 것이고, 이는 유지보수성을 좋게 만들고 향후 변경에 대해서 유연하게 대처할 수 있기 때문입니다.

- 캡슐화의 장점
  - 시스템 개발 속도를 높인다. 여러 컴포넌트를 병렬로 개발 가능
  - 시스템 관리 비용을 낮춘다.
  - 성능 최적화에 도움을 준다.
  - 재사용성을 높인다.
  - 큰 시스템을 제작하는 난이도를 낮춰준다.
  - decoupling

## 접근 지시자

- `private` : 멤버를 선언한 톱레벨 클래스에서만 접근할 수 있다.
- `package-private(default)` : 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. 접근 제한자를 명시하지 않았을 때 적용되는 패키지 접근 수준이다(단, 인터페이스의 멤버는 기본적으로 public 이 적용된다.)
- `protected` : package-private 의 접근 범위를 포함하며, 이 멤버를 선언한 클래스의 하위 클래스에서도 접근할 수 있다.
- `public` : 모든 곳에서 접근할 수 있다.

## public static final 배열 필드 금지

클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안된다. 이런 필드나 접근자를 제공한다면 클라이언트에서 그 배열의 내용을 수정할 수 있게 됩니다.

```java
// 보안 허점 존재
public static final Thing[] VALUES = { ... };
```

- 해결책 1. public 배열을 private 으로 만들고 public 불변 리스트 추가

```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUE));
```

- 해결책 2. 배열을 private 으로 만들고 그 복사본을 반환하는 public 메서드 추가

```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final Thing[] values() {
  return PRIVATE_VALUES.clone();
}
```

## 정리

- 캡슐화는 getter/setter 의 역할을 하는 public 메서드를 만들고, 속성을 private 으로 감추어 다른 코드들과의 결합도를 낮추기
위한 객체지향 3대 요소 중 하나이다.

## 참고

> [Effective Java 3](#)