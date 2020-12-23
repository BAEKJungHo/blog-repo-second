---
title: "@Transactional"
layout: post
category: Spring
tags: [Spring, DataBase]
excerpt: "스프링의 @Transactional 과 트랜잭션에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

# 서론

스프링에서 사용하는 @Transactional 어노테이션과, 트랜잭션이 무엇이고, 트랜잭션을 거는 Layer 는 어디가 적당한지 등에 대해서 다뤄보겠습니다.

# @Transactional

## 트랜잭션이란?

일련의 작업을 한 단위로 묶고, 모든 작업은 한 번에 커밋되거나 롤백되어야 한다.

## 트랜 잭션의 성질(ACID)

- 원자성(Atomicity)
    - 한 트랜잭션 내에서 실행한 작업들은 하나로 간주한다. 즉, 모두 성공 또는 모두 실패.
- 일관성(Consistency)
    - 트랜잭션은 일관성 있는 데이타베이스 상태를 유지한다. (data integrity 만족 등.)
- 격리성(Isolation)
    - 동시에 실행되는 트랜잭션들이 서로 영향을 미치지 않도록 격리해야한다.
- 지속성(Durability)
    - 트랜잭션을 성공적으로 마치면 결과가 항상 저장되어야 한다.

## 회원가입과 마일리지

`회원가입과 마일리지 적립`을 예로 들어 보겠습니다.

- 요구사항
    - 회원가입시 마일리지 적립 3천 포인트

회원 가입을 위해서 UserService 에서 `join 메서드를 호출(회원가입)`하고, 마일리지 적립을 위해서 MileageService 에서 `accumulate 메서드를 호출(마일리지 적립)`해야 합니다.

- 일련의 작업
    - join 메서드 호출
    - accumulate 메서드 호출

만약에 이러한 일련의 작업을 Controller 에서 구현해보겠습니다.

```java
@RequriedArgsConstructor
@RequestMapping("/user")
@Controller
public class UserController {

    private final UserService userService;
    private final MileageService mileageService;

    @PostMapping("/join")
    public String join(
        @ModelAttribute UserVO userVO,
        BindingResult bindingResult
    ) {
        // 생략
        userService.join(userVO);
        mileageService.accumulate(userVO);
        // 생략
    }

}
```

이렇게 일련의 작업들이 컨트롤러에 구현되어 있는 경우에는 만약에 join 은 성공했지만 accumulate 메서드 호출 시 에러가 발생해서 실패하게 되면 회원가입은 되었지만 마일리지 적립은 안되는 현상이 발생하게 됩니다.

이렇게 일련의 작업을 하나의 단위로 묶어야 하는경우 `트랜잭션`의 기능을 사용해야 합니다.

스프링에서는 트랜잭션 처리를 지원하는데 그 중 어노테이션 방식으로 `@Transactional` 을 선언하여 사용하는 방법이 일반적이고 이러한 방식을 `선언적 트랜잭션` 이라고 합니다.

여기서 올바른 개발자는 아마 이런 생각을 할 것입니다.

"@Transcational 을 컨트롤러의 핸들러 메서드 위에 선언하면 되는거 아니야?" 라는 의문을 가질 수 있습니다. 

그럼 바로 이어서 해당 의문을 해소 시켜봅시다.

## 트랜잭션은 어느 Layer 에서 처리해야할까?

### @Transcational 을 DAO(Repository)에 추가하는 경우

DAO에 추가하고 서비스에서 2 개의 DAO 클래스를 호출한다고 가정하고 하나는 실패하고 다른 하나는 성공하는 경우 @Transactional 이 서비스에 있지 않으면 하나의 DB 가 커밋되고 다른 하나는 롤백됩니다. 따라서 DAO 두는건 적합하지 않습니다.

### @Transcational 을 Controller 에 추가하는 경우

컨트롤러에 @Transcational 을 추가하는 경우, 비지니스 로직에 관한 코드들이 컨트롤러 쪽에 모여있을 가능성이 큽니다.
비지니스 로직이 컨트롤러에 모여있는경우, __해당 코드를 다른 곳에서 똑같이 재사용하고 싶어도 하지 못하고, 결국엔 중복 코드가 생기기 마련입니다.__ 따라서 보통 비지니스 로직에 관한 코드들은 Service 쪽에 작성하는 것이 바람직합니다. 

또한, 컨트롤러 자체는 `Presentation` 계층입니다. 컨트롤러는 데이터가 처리된 결과를 Model 에 담아서 View 에 전달하는 역할이 핵심입니다. 따라서 `MVC 계층 구조 관계`로 볼때에도 적합하지 않습니다.

## @Transcational 을 Service Interface 에 추가하는 경우

Service Interface 에 추가하는 경우에, 구현체에서 해당 인터페이스를 구현해서 사용하는데 오버라이딩한 구현체 메서드에서는 트랜잭션이 적용되지 않습니다. 어노테이션은 상속이 불가능하기 때문에 Service Interface 에 두는것도 적합하지 않습니다.

> Spring recommends that you only annotate concrete classes (and methods of concrete classes) with the @Transactional annotation, as opposed to annotating interfaces. You certainly can place the @Transactional annotation on an interface (or an interface method), but this works only as you would expect it to if you are using interface-based proxies. `The fact that Java annotations are not inherited from interfaces` means that if you are using class-based proxies (proxy-target-class="true") or the weaving-based aspect (mode="aspectj"), then the transaction settings are not recognized by the proxying and weaving infrastructure, and the object will not be wrapped in a transactional proxy, which would be decidedly bad.
>
> Spring은 인터페이스에 주석을 달지 않고 @Transactional 주석으로 구체적인 클래스 (및 구체적인 클래스의 메서드)에만 주석을 달도록 권장합니다. 인터페이스 (또는 인터페이스 메소드)에 @Transactional 어노테이션을 배치 할 수 있지만 이는 인터페이스 기반 프록시를 사용하는 경우 예상 한 대로만 작동합니다. Java 주석이 인터페이스에서 상속되지 않는다는 사실은 클래스 기반 프록시 (proxy-target-class = "true") 또는 위빙 기반 측면 (mode = "aspectj")을 사용하는 경우 트랜잭션 설정이 다음과 같음을 의미합니다. 프록시 및 위빙 인프라에 의해 인식되지 않으며 객체가 트랜잭션 프록시에 래핑되지 않으므로 확실히 나쁠 것입니다.
>
> 출처 : https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/transaction.html

## @Transcational 을 Service 구현체에 추가하는 경우

이 경우가 가장 바람직한 경우입니다. 일단 Service 구현체에는 각종 비지니스 로직이 모여있으며, `역할 구현 책임` 이라는 객체지향 관점에서 바라볼 때에도 Service 구현체에서 객체들이 서로 다양한 관계를 맺고 각자 알맞은 책임을 수행하고 있을 가능성이 큽니다.

또한 스프링에서는 Service 구현체에 트랜잭션 처리 하는것을 선호하고 있습니다.

아래 코드는 [스프링 docs](https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/transaction.html) 에서 발췌한 샘플 코드 입니다.

```java
@Transactional(readOnly = true)
public class DefaultFooService implements FooService {

  public Foo getFoo(String fooName) {
    // do something
  }

  // these settings have precedence for this method
  @Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW)
  public void updateFoo(Foo foo) {
    // do something
  }
}
```

## 트랜잭션 전파

- REQUIRED (기본값)
    - 부모 트랜잭션이 존재한다면 부모 트랜잭션으로 합류합니다. 부모 트랜잭션이 없다면 새로운 트랜잭션을 생성합니다.
    - 중간에 롤백이 발생한다면 모두 하나의 트랜잭션이기 때문에 진행사항이 모두 롤백됩니다.
- REQUIRES_NEW
    - 새 트랜잭션을 만들고 현재 트랜잭션이 있으면 일시 중단합니다. 각각의 트랜잭션이 롤백되더라도 서로 영향을 주지 않습니다.
- MANDATORY
    - 부모 트랜잭션에 합류합니다. 만약 부모 트랜잭션이 없다면 예외를 발생시킵니다.
- NESTED
    - 부모 트랜잭션이 존재한다면 중첩 트랜잭션을 생성합니다. 중첩된 트랜잭션 내부에서 롤백 발생시 해당 중첩 트랜잭션의 시작 지점 까지만 롤백됩니다. 중첩 트랜잭션은 부모 트랜잭션이 커밋될 때 같이 커밋됩니다.
    - 부모 트랜잭션이 존재하지 않는다면 새로운 트랜잭션을 생성합니다.
- NEVER
    - 트랜잭션을 생성하지 않습니다. 부모 트랜잭션이 존재한다면  예외를 발생시킵니다.
- SUPPORTS
    - 부모 트랜잭션이 있다면 합류합니다. 진행중인 부모 트랜잭션이 없다면 트랜잭션을 생성하지 않습니다.
- NOT_SUPPORTED
    - 부모 트랜잭션이 있다면 보류시킵니다. 진행중인 부모 트랜잭션이 없다면 트랜잭션을 생성하지 않습니다.

## 트랜잭션 롤백

- @Transactional(rollbackFor = Exception.class)
- @Transactional(rollbackFor = {RuntimeException.class, Error.class})
    - `스프링은 RuntimeException 과 Error를 기본적으로 롤백 정책으로 이해합니다.`
- @Transactional(noRollbackFor={IgnoreRollbackException.class}) : 해당 Exception 이 발생하면 Rollback 을 하지 않습니다.

스프링 프레임워크에서 @Transactional 어노테이션을 사용한 트랙잭션 처리에서 Checked 예외는 롤백 되지 않습니다. 이것은 스프링 프레임워크가 EJB 에서의 관습을 따르기 때문이라고 합니다. 그러므로 기본적으로 Unchecked 예외는 롤백이 됩니다. 따라서 RuntimeException 을 던지면 롤백이 된다는 것입니다.

또한 Service 구현체에서 insert 혹은 각종 쿼리를 실행하다가 쿼리에 문제가 생겨서 에러가 발생해도 롤백이 되는 현상을 볼 수 있는데 그 이유는 스프링이 `JdbcTemplate 를 도입`하면서 `SQLException 을 DataAccessExcpetion(Unchecked Exception)` 으로 던지기 때문에 쿼리에서 문제가 발생해서 터져도 롤백이 됬던 것입니다.

## AbstractPlatformTransactionManager

AbstractPlatformTransactionManager.java 의 `setGlobalRollbackOnParticipationFailure` 메서드를 살펴보면 다음과 같이 주석이 되어있습니다. 번역은 `파파고`의 힘을 빌렸습니다. 따라서 오역이 있을 수 있습니다.

> Set whether to globally mark an existing transaction as rollback-only after a participating transaction failed.
> 
> 참여 트랜잭션이 실패한 후 기존 트랜잭션을 rollback-only 로 전역으로 표시할지 여부를 설정합니다.
>
> Default is “true”: If a participating transaction (e.g. with PROPAGATION_REQUIRED or PROPAGATION_SUPPORTS encountering an existing transaction) fails, the transaction will be globally marked as rollback-only. The only possible outcome of such a transaction is a rollback: The transaction originator cannot make the transaction commit anymore.
> 
> 기본값은 "true"입니다. 참여 트랜잭션(예: PREPATION_REquired 또는 PREPATION_SPORTUST가 기존 트랜잭션을 발견함)이 실패하면 트랜잭션은 롤백 전용으로 전체적으로 표시됩니다. 이러한 트랜잭션의 가능한 결과는 롤백뿐입니다. 트랜잭션 작성자는 트랜잭션을 더 이상 커밋할 수 없습니다.
> 
> Switch this to “false” to let the transaction originator make the rollback decision. If a participating transaction fails with an exception, the caller can still decide to continue with a different path within the transaction. However, note that this will only work as long as all participating resources are capable of continuing towards a transaction commit even after a data access failure: This is generally not the case for a Hibernate Session, for example; neither is it for a sequence of JDBC insert_update_delete operations.
> 
> 트랜잭션 생성자가 롤백 결정을 내리려면 이 설정을 "false"로 전환하십시오. 참여 트랜잭션이 예외로 실패하더라도 호출자는 여전히 트랜잭션 내에서 다른 경로를 계속하기로 결정할 수 있습니다. 그러나 이는 데이터 액세스 실패 후에도 모든 참여 리소스가 트랜잭션 커밋을 계속 수행할 수 있는 경우에만 작동합니다. 예를 들어 일반적으로 최대 절전 모드 세션의 경우는 그렇지 않습니다. JDBC insert_update_delete 작업 순서도 마찬가지입니다.
>
> Note:This flag only applies to an explicit rollback attempt for a subtransaction, typically caused by an exception thrown by a data access operation (where TransactionInterceptor will trigger a PlatformTransactionManager.rollback() call according to a rollback rule). If the flag is off, the caller can handle the exception and decide on a rollback, independent of the rollback rules of the subtransaction. This flag does, however, not apply to explicit setRollbackOnly calls on a TransactionStatus, which will always cause an eventual global rollback (as it might not throw an exception after the rollback-only call).
>
> 참고: 이 플래그는 일반적으로 데이터 액세스 작업에서 발생하는 예외(트랜잭션)로 인해 발생한 하위 트랜잭션에 대한 명시적 롤백 시도에만 적용됩니다.인터셉터는 롤백 규칙에 따라 PlatformTransactionManager.rollback() 호출을 트리거합니다. 플래그가 꺼져 있으면 호출자는 예외를 처리하고 하위 트랜잭션의 롤백 규칙과 관계없이 롤백을 결정할 수 있습니다. 그러나 이 플래그는 트랜잭션 상태에 대한 명시적 setRollbackOnly 호출에는 적용되지 않으며, 이로 인해 항상 전체 롤백이 발생합니다(롤백 전용 호출 후 예외가 발생하지 않을 수 있음).
> 
> The recommended solution for handling failure of a subtransaction is a “nested transaction”, where the global transaction can be rolled back to a savepoint taken at the beginning of the subtransaction. PROPAGATION_NESTED provides exactly those semantics; however, it will only work when nested transaction support is available. This is the case with DataSourceTransactionManager, but not with JtaTransactionManager.
> 
> 하위 트랜잭션의 실패를 처리하기 위해 권장되는 솔루션은 "중첩 트랜잭션"입니다. 여기서 글로벌 트랜잭션을 하위 트랜잭션 시작 시 사용된 저장 지점으로 롤백할 수 있습니다. PROPADION_NSEST는 이러한 의미론을 정확하게 제공하지만, 중첩된 트랜잭션 지원을 사용할 수 있을 때만 작동합니다. 이는 DataSourceTransaction Manager의 경우이지만 JtaTransaction Manager의 경우는 그렇지 않습니다.
>
> 출처 : [spring-framework transaction github](https://github.com/spring-projects/spring-framework/blob/4560dc2818ae1d5e1bc5ceef89f1b6870700eb1f/spring-tx/src/main/java/org/springframework/transaction/support/AbstractPlatformTransactionManager.java#L265)

## 참고

- https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/transaction.html
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Propagation.html
- https://woowabros.github.io/experience/2019/01/29/exception-in-transaction.html
- https://deveric.tistory.com/86
- https://pjh3749.tistory.com/269