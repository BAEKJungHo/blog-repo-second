---
title: "폼 객체 vs 엔티티 직접 사용"
layout: post
category: Spring
tags: [Spring]
excerpt: "폼 객체 vs 엔티티 직접 사용 어느 것이 더 나을까?"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/SPRING.jpg)

# 서론

화면단에서 등록을 하기 위해 폼 객체를 따로 만들어 사용하는 방법과 엔티티를 직접 사용하는 방법 어느 것이 나을지 배워보겠습니다.

# 폼 객체 vs 엔티티 직접 사용

요구사항이 정말 단순할 때는 폼 객체( XXXForm.java or XXXDTO.java ) 없이 엔티티(EX. Member.java)를 직접 등록과 수정 화면에서 사용해도 된다.
하지만 화면 요구사항이 복잡해지기 시작하면, 엔티티에 화면을 처리하기 위한 기능이 점점 증가한다.

`결과적으로 엔티티는 점점 화면에 종속적으로 변하고, 이렇게 화면 기능 때문에 지저분해진 엔티티는 결국 유지보수하기 어려워진다.`

실무에서 엔티티는 핵심 비즈니스 로직만 가지고 있고, 화면을 위한 로직은 없어야 한다. 화면이나 API에 맞는 폼 객체나 DTO를 사용하자. 그래서 화면이나 API 요구사항을 이것들로 처리하고, 엔티티는 최대한 순수하게 유지하자.

- Entity : Member.java

```java
@GetMapping(value = "/members")
public String list(Model model) {
    List<Member> members = memberService.findMembers();
    model.addAttribute("members", members);

    return "members/memberList";
}
```

여기서 엔티티를 템플릿 엔진에 넘기는데, 이런 경우에는 엔티티를 넘겨도 상관은 없다. 템플릿 엔진에서 랜더링 하는 경우에는 어차피 서버에서 기능이 돌아가서 필요한 데이터만 찍어서 출력하기 때문이다. 그래도 가장 깔끔한 방식은 DTO 를 만들어서 처리하는 것이다. (시스템이 커질 수록 이런 작은 습관 하나하나가 큰 영향을 미친다.)

하지만, `API 의 경우에는 이유를 불문하고 무조건 DTO 를 만들어서 처리해야한다. (API 의 경우 절대 엔티티를 외부로 반환하면 안된다.)`

API 라는 것은 스펙(spec)인데, 만약에 엔티티에 password 라는 필드가 추가된 경우, 패스워드가 그대로 노출 될 수있고, API spec 이 변해버린다.

## 참고

> [인프런. 스프링 부트와 JPA 활용](#)