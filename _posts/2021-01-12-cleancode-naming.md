---
title: "클린 코드를 위한 좋은 네이밍 방법"
layout: post
category: CleanCode
tags: [CleanCode]
excerpt: "클린 코드를 위한 좋은 네이밍 방법에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

# 서론

좋은 네이밍을 짓는것은 프로그래머의 책임입니다.

이번에는 좋은 네이밍을 짓는 방법에 대해서 배워보겠습니다.

# 좋은 코드란?

좋은 코드란 읽기 쉬운 즉, `가독성이 좋은` 코드를 말합니다. 좋은 네이밍은 코드를 읽기 쉽게 만들어 다른 개발자들과의 협업을
원할하게 해줍니다. 즉, 유지보수가 편리해집니다.

__좋은 코드는 로직 파악에 충분한 정보를 담고 있어야 하며, 별다른 주석이나 문서 없이 코드로 이해가 된다면 좋은 코드라고 할 수 있습니다.__

# 주석(Comment)

한 프로그램을 설명하기 위해서 주석을 주구장창 다는 것은 `안티패턴`입니다. 로직이 상당히 꼬여있고 복잡한 경우에 해당 로직에 대한 설명이 필요한 경우는 문서화해서 관리해두는 편이 좋습니다.

최대한 `좋은 네이밍`을 통해서 가독성 좋은 코드를 만들어서 꼭 필요한 경우에만 주석을 적절히 다는 개발자가 좋은 개발자라 할 수 있습니다.

# 좋은 네이밍 짓기

## 패키지 네이밍

패키지명은 소문자로 시작한다.

## 변수 네이밍

- 대소문자는 구분되며 길이의 제한은 없다.
- 숫자로 시작하면 안 된다.
- 특수문자는 _ 와 $ 만 허용한다.
- 예약어를 사용해서는 안 된다.

### J2EE 에서 권장하는 변수 명명 규칙

- 변수는 첫 글자의 소문자로 시작하는 명사로 짓는다.
- 여러 단어로 이루어진 이름인 경우 각 단어의 첫 글자를 대문자로 한다. (카멜 표기법. Camel Case)

### 변수명은 변경 가능성을 생각해서 작성

변경 가능성을 생각해서 변수명을 작성하라는 것이 무슨 의미일까?

예제로 확인해 봅시다.

```java
List<User> userList = userService.findAllUser();
```

변수명에 자료형 이름이 들어갔습니다.

만약 해당 자료형이 Map 으로 바뀐다면 변수명도 바꿔야합니다. 즉, 자료형은 변경 가능성이 있기 때문에 위와 같은 경우는 복수를 나타내는 `s` 를 붙여서 네이밍 하는 것이좋습니다.

```java
List<User> users = userService.findAllUser();
```

### 축약어는 금지

DB 나 Java 나 축약어를 좋아하는 사람이 그렇게 있습니다...

심지어 주석도 달지 않은채 방치해두는 경우도 많습니다...

물론 축약어를 사용해도 되는 경우도 있는데 `해당 축약어가 자주 사용되어 누가 봐도 알아 볼 수 있는경우, 의미가 명확한 경우`에 사용 가능합니다.

```java
private String ap_sts;
```

위 변수명을 보고 무슨 역할을 하는지 해석이 가능할까요? 대부분의 사람들은 `sts`는 상태를 나타낸다라고만 알고 `ap`가 무엇을 의미하는지 모를겁니다. ap 가 apple 사과 인지, airplane 비행기인지, apply 신청인지 approve 승인인지 알 방법이 없습니다.

즉, 축약어 `sts`는 사용 가능한 축약어이며, `ap` 는 사용해서는 안되는 축약어 즉, 잘못된 네이밍을 했습니다.

그리고 위 변수 네이밍에는 `스네이크 케이스(snake-case)`를 적용했는데, 개발하는 프로젝트 마다 스네이크 케이스로할 건지 카멜 케이스로 할 건지 다릅니다. 

> 가급적 카멜 케이스를 기본으로 하는것을 추천합니다. J2EE 의 권장 사항이기도 합니다.

그러면 다시 올바른 변수 네이밍을 보겠습니다.

```java
// private String applyStatus;
private String applySts;
```

축약어를 사용하게되면 `변수명이 자신의 역할을 설명하는데 충분한 정보를 포함하고 있지 않을 가능성이 매우 크므로` 축약어 사용을 가급적 멀리해야합니다.

### 정리

- 변수명은 변경 가능성을 생각해서 작성해야한다.
- 변수명은 축약어는 가급적 피해야하며, 축약어를 사용할 수 있는 경우는 누가 봐도 이해할 수 있는 경우에만 사용해야한다.
- 변수명은 다른 개발자들이 봐도 무슨 역할을 하는지 알 수 있을 정도의 충분한 정보를 포함하고 있어야 한다.
- J2EE 는 변수 네이밍 시 카멜 케이스를 권장한다.

## 메서드 네이밍

- 메서드 이름은 동사/전치사로 시작한다.
- A 객체에서 B 객체로 변환하는 메서드나 다른 빌더패턴의 경우 전치사를 사용한다.

> 메서드 네이밍도 위에서 배운 변수 네이밍과 동일하게 `축약어 금지`, `충분한 정보를 포함`, `변경 가능성을 생각` 하여 네이밍을 해야 합니다.

### 자주 사용되는 접두어 동사

- `init`
    - 데이터 초기화 시 사용
    - return void

```java
// User 클래스에서 시퀀스 초기화
user.initSeq();
```

- `is`
    - 맞는지 틀린지
    - return boolean

```java
// 사용자가 관리자가 맞는지
if(user.isAdmin()) {}
```

- `has`
    - 가지고 있는지 아닌지
    - return boolean

```java
// 사용자가 핸드폰을 가지고 있는지 
if(user.hasPhone()) {}
```

- `can`
    - 할 수 있는지 아닌지
    - return boolean

```java
// 사용자가 회원 가입을 할 수 있는지
if(user.canJoin()) {}
```

- `create`
    - 새로운 객체를 생성해 리턴해 주는 경우
    - 팩터리 메서드 패턴에서 주로 사용 
    - 혹은 비지니스 로직 과정 중, 새로운 객체를 생성해야할 때 private 메서드로 빼내서 사용
    - return Object

```java
@Override
public void join(User user, PrivacyInfo privacyInfo) {
    userRepsitory.join(createUser(user, priavcyInfo));
}

private User createUser(User user, PrivacyInfo privacyInfo) {
    // 필요한 데이터들 set 해서 new user 생성
}
```

- `find`
    - 데이터를 조회할 때 사용
    - find 가 접두어로 붙은 경우 데이터가 존재할 수 도 안 할 수도있다.
    - return Object

```java
@Override
public List<User> findAllUser(User user) {
    return userRepsitory.findAllUser(user);
}
```

- `get`
    - 데이터를 가져올 때 사용
    - get 이 접두어로 붙은 경우 데이터가 존재해야한다.
    - return Object
- `set`
    - 데이터를 설정할 때 사용
    - return void

```java
public class User {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name){
        return this.name = name;
    }

}
```

- `to` 
    - 해당 객체를 다른 객체로 변환할 때 사용
    - return Object

```java
public List<Article> findMoreAritcle(ArticleDto.FindRequest dto) {
    // toEntity 를 통해서 Aritlce 객체로 변환
    articleRpsitory.findMoreAritcle(dto.toEntity());
}
```

- `A by B`
    - B 에 의해서 A 를 수행
    - return Object

```java
@Override
public List<User> findAllUserBySearch(User user) {
    // 검색을 통해 나온 사용자 목록을 조회
    return userRepsitory.findAllUserBySearch(user);
}
```

## 클래스 네이밍

클래스와 인스턴스명은 `명사`로 짓습니다.

## 네이밍시 피해야 하는 안티패턴

- `축약어 사용을 피해라`
    - 축약어를 사용하게 되면 변수명에 충분한 정보를 포함하고 있지 않아 가독성을 떨어뜨리고, 유지보수를 어렵게 한다.
    - 오히려 불충분한 정보를 가지고 있는 변수명 보다, 충분한 정보를 가지고 있는 긴 이름의 변수명이 더 낫다.
- `난해함을 피해라`
    - 난해한 전문용어와 같은 네이밍을 피해야한다. 해당 변수명이 무슨역할인지 알아보기 위해서 검색을 시도할 것이다.
    - 나만 아는 이상한 변수명은 피해야한다.
- `중복을 피해라`
     - 클래스 명에서 의미를 유추할 수 있으면 메서드 명이나 변수명에서는 중복을 제거하는게 더 낫다.
- `길다고 다 좋은 것은 아니다`
    - 불필요하게 너무 긴 이름은 가독성을 떨어뜨리기 때문에 좋지 않다. 만약 사용한 이름이 너무 긴 경우 빼도 되는 단어가 있는지 고민해봐야 한다.
    - 함수 이름에 만약 파라미터나 리턴 타입 같은 다른 시그니처 구성 요소와 중복되는 단어가 있다면 그 단어를 제거하는 것이다.
- `일관성 있게 지어라`
    - 이름을 지을 때는 꼭 팀내의 룰이 없더라도 동일 패키지나 동일 프로젝트(모듈)내의 다른 클래스, 함수, 변수의 이름과 어느 정도 일관성이 있게 작성하는 것이 좋다.

> 좋은 이름 작성에 오랜 시간이 걸린다고 해서 조급해하거나 자책감을 느끼면 안된다. 클린 아키텍처 에서 가장 빨리가는 길은 정확하게 가는 것이라고 했다. 이름 짓기에 투자한 시간 만큼 좋은 코드가 나오니, 미래를 생각했을때 자기자신과 동료가 편해진다.

## 참고

> [Woowacourse 좋은 코드를 위한 자바 변수명 짓기](https://woowacourse.github.io/javable/post/2020-04-24-variable_naming/)
>
> [Woowacourse 좋은 코드를 위한 자바 메서드 네이밍](https://woowacourse.github.io/javable/post/2020-04-26-Method-Naming/)
>
> [인프런. 당신을 함께 일하고 싶은 개발자로 만들어 줄 클린 코드 작성법](#)