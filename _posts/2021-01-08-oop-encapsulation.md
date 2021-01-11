---
title: "[객체지향 3대 요소] 캡슐화(Encapsulation)"
layout: post
category: Spring
tags: [Spring]
excerpt: "객체지향 3대 요소인 캡슐화에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

# 서론

객체지향 3대 요소인 캡슐화(Encapsulation)에 대해서 배워 보겠습니다.

# 캡슐화(Encapsulation)

캡슐화란 내부 `정보를 감추고` 외부의 변경에 `유연`하게 대처하기 위한 객체지향 요소입니다.

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

## 정리

- 캡슐화는 getter/setter 의 역할을 하는 public 메서드를 만들고, 속성을 private 으로 감추어 다른 코드들과의 결합도를 낮추기
위한 객체지향 3대 요소 중 하나이다.