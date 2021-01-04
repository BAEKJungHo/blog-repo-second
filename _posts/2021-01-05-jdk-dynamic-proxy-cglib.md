---
title: "[Proxy 와 스프링 AOP] 1. JDK Dynamic Proxy 와 CGLib 라이브러리"
layout: post
category: Spring
tags: [Spring]
excerpt: "JDK Dynamic Proxy 와 CGLib 라이브러리에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

# 서론

JDK Dynamic Proxy 와 CGLib 라이브러리에 대해 배워보겠습니다. 

# 프록시(Proxy) 란?

프록시 디자인 패턴이란 어떤 객체에 대한 접근을 제어하기 위한 용도로 대리인이나 대변인에 해당하는 객체를 제공하는 패턴을 의미합니다.

프록시(Proxy) 란 쉽게말해 대리자, 대변인 이라고 생각하면됩니다. 

## Proxy Design Pattern

- 프록시와 리얼 서브젝트가 공유하는 인터페이스가 있고, 클라이언트는 해당 `인터페이스 타입으로 프록시를 사용`한다.
- `클라이언트는 프록시를 거쳐서 리얼 서브젝트를 사용`하기 때문에 프록시는 리얼 서브젝트에 대한 접근을 관리하거나 부가기능을 제공하거나, 리턴값을 변경할 수도 있다.
- 리얼 서브젝트는 자신이 해야할 일만 하면서(SRP) 프록시를 사용해서 부가적인 기능(접근 제한, 로깅, 트랜잭션 등)을 제공할 때 이런 패턴을 주로 사용한다.
- 원래 하려던 기능을 수행하며 그외의 부가적인 작업(로깅, 인증, 네트워크 통신 등)을 수행하기에 좋다.
- 비용이 많이 드는 연산(DB 쿼리, 대용량 텍스트 파일 등)을 실제로 필요한 시점에 수행할 수 있다.
- 사용자 입장에서는 프록시 객체나 실제 객체나 사용법은 유사하므로 사용성이 좋다.

![proxy](/images/posts/202101/proxy.JPG)

### Example

```java
/*
 * Subject
 */
public interface UserService {
    void buy(User user);
}

/*
 * Real Subject
 */
public class DefaultUserService implements UserService { 
  public void buy(Goods goods) {
    System.out.println("buy" + goods.getTitle());
  }
}

/*
 * Proxy
 */
public class UserServiceProxy implements UserService {
  UserService userService;
  
  public UserServiceProxy(UserService userService) {
    this.userService = userService;
  }
  
  @Override
  public void rent(Goods goods) {
    System.out.println("buy cap");
    userService.buy(goods);
    System.out.println("buy computer");
  }

}

/*
 * Client
 */
public class UserServiceTest {
  
  /*
   * UserService : 서브젝트
   * UserServiceProxy : 프록시
   * DefaultUserService : 리얼 서브젝트
   */
  UserService userService = new UserServiceProxy(new DefaultUserService());
  
  /*
   * 결과 
   * buy cap
   * buy Head First Design Pattern
   * buy computer
   */
  @Test
  public void di() {
    Goods goods = new Goods();
    goods.setTitle("Head First Design Pattern");
    userService.buy(goods);
  }
  
}
```

# JDK Dynamic Proxy

자바에는 프록시 기능이 내장되어 있습니다. `java.lang.reflect` 에 들어있습다. 실제 프록시 클래스는 실행 중에 생성되기 때문에 이러한 자바 기술을 `동적 프록시(dynamic proxy)`라고 부릅니다.

JDK Dynamic Proxy 는 JDK 1.3+ 부터 제공되는 Proxy Factory 에 의해 런타임 시 동적으로 만들어 지는 오브젝트입니다. JDK Dynamic Proxy 는 반드시 Interface 가 정의 되어있고, Interface 에 대한 명세를 기준으로 Proxy 를 생성합니다.

내부적으로 동적 프록시에서는 `InvocationHandler` 라는 Interface 를 구현하여 만들어지는데 InvocationHandler의 `invoke` 라는 메서드를 Override 하여 Proxy 의 위임 기능을 수행합니다. 이 과정에서 Object 에 대해 `Reflection` 기능을 사용하여 기능을 구현하기 때문에 퍼포먼스의 하락의 원인이 되기도 합니다.

## JDK InvocationHandler Interface

- invoke() 메서드 하나만 가지고 있다.
- 프록시의 어떤 메서드가 호출되든 무조건 핸들러의 invoke() 메서드가 호출된다.

```java
package java.lang.reflect;

/**
 * {@code InvocationHandler} is the interface implemented by
 * the <i>invocation handler</i> of a proxy instance.
 *
 * <p>Each proxy instance has an associated invocation handler.
 * When a method is invoked on a proxy instance, the method
 * invocation is encoded and dispatched to the {@code invoke}
 * method of its invocation handler.
 *
 * @author      Peter Jones
 * @see         Proxy
 * @since       1.3
 */
public interface InvocationHandler {

    /**
     * Processes a method invocation on a proxy instance and returns
     * the result.  This method will be invoked on an invocation handler
     * when a method is invoked on a proxy instance that it is
     * associated with.
     *
     * @param   proxy the proxy instance that the method was invoked on
     *
     * @param   method the {@code Method} instance corresponding to
     * the interface method invoked on the proxy instance.  The declaring
     * class of the {@code Method} object will be the interface that
     * the method was declared in, which may be a superinterface of the
     * proxy interface that the proxy class inherits the method through.
     *
     * @param   args an array of objects containing the values of the
     * arguments passed in the method invocation on the proxy instance,
     * or {@code null} if interface method takes no arguments.
     * Arguments of primitive types are wrapped in instances of the
     * appropriate primitive wrapper class, such as
     * {@code java.lang.Integer} or {@code java.lang.Boolean}.
     *
     * @return  the value to return from the method invocation on the
     * proxy instance.  If the declared return type of the interface
     * method is a primitive type, then the value returned by
     * this method must be an instance of the corresponding primitive
     * wrapper class; otherwise, it must be a type assignable to the
     * declared return type.  If the value returned by this method is
     * {@code null} and the interface method's return type is
     * primitive, then a {@code NullPointerException} will be
     * thrown by the method invocation on the proxy instance.  If the
     * value returned by this method is otherwise not compatible with
     * the interface method's declared return type as described above,
     * a {@code ClassCastException} will be thrown by the method
     * invocation on the proxy instance.
     *
     * @throws  Throwable the exception to throw from the method
     * invocation on the proxy instance.  The exception's type must be
     * assignable either to any of the exception types declared in the
     * {@code throws} clause of the interface method or to the
     * unchecked exception types {@code java.lang.RuntimeException}
     * or {@code java.lang.Error}.  If a checked exception is
     * thrown by this method that is not assignable to any of the
     * exception types declared in the {@code throws} clause of
     * the interface method, then an
     * {@link UndeclaredThrowableException} containing the
     * exception that was thrown by this method will be thrown by the
     * method invocation on the proxy instance.
     *
     * @see     UndeclaredThrowableException
     */
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

- Real Invocation Handler Example

```java
import java.lang.reflect.*;

public class OwnerInfocationHandler implements InvocationHandler {
  PersonBean person;
  
  public OwnerInfocationHandler(PersonBean person) {
    this.person = person;
  }
  
  public Object invoke(Object proxy, Method method, Object[] args) throws IllegalAccessException {
    try {
      if(method.getName().startsWith("get")) {
        return method.invoke(person, args); // Getter 메서드의 경우 주 객체의 메서드 호출
      } else if(method.getName().equals("setHotOrNotRating")) {
        throw new IllegalAccessException();
      } else if(method.getName().startsWith("set")) {
        return method.invoke(person, args); // Setter 메서드의 경우 주 객체의 메서드 호출
      } 
    } catch(InvocationTargetException e) { // 진짜 Real Subject 에서 예외를 던졌을 때
      e.printStackTrace();
    }
    return null; // 다른 메서드가 호출되는 경우
  }
}
```

## JDK Dynamic proxy Example

프록시 패턴을 사용하다 보면 코드 중복 문제가 많이 발생할 수 있는데, 이것을 해결하기 위한것이 다이나믹 프록시입니다.

- 런타임에 특정 인터페이스들을 구현하는 클래스 또는 인스턴스를 만드는 기술
- 프록시 인스턴스 만들기
- Object Proxy.newPorxyInstance(ClassLoader, Interfaces, InvocationHandler)

```java
/**
 * new Class[]{UserService.class} <-- 어떤 인터페이스의 프록시 타입인지 적어야한다.
 */
public class UserServiceTest {
  UserService userService = (UserService) Proxy.newPorxyInstance(UserService.class.getClassLoader(), new Class[]{UserService.class},
    new InvocationHandler() {
       UserService userService = new DefaultUserService(); // 리얼 서브젝트

       /**
        * 해당 메서드에 리얼 서브젝트가 할일을 제외한 부가적인 기능을 넣거나 하면된다.
        */
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          // return method.invoke(userService, args); -> 아무것도 하지않는, 리얼 서브젝트의 기능만 동작하게하는 프록시
          
          // buy 메서드에만 부가적인 기능 동작하게 하는 프록시 적용하고 싶을 때
          if(method.getName().equals("buy")) {
               System.out.println("buy cap"); // 부가적인 기능
               Object invoke = method.invoke(userService, args);
               System.out.println("buy computer"); // 부가적인 기능
               return invoke;
          }
           
          return method.invoke(userService, args);
       }
    }
});
```

다이나믹 프록시의 단점은 클래스 기반 프록시를 만들지 못합니다. 그리고 위 코드는 유연하지 못한 코드이며 스프링에서 등장한 것이 스프링 AOP 입니다. 
그래서 스프링 AOP 를 프록시 기반 AOP 라고 부르는 것입니다.

다이나믹 프록시는 Spring AOP 의 AOP 기술의 근간이 되는 방식이기 때문에 Spring 에서 사용되는 AOP 의 기술들은 Proxy 메커니즘을 따르고 있습니다.

- 다이나믹 프록시가 사용되는 곳
    - 스프링 JPA
    - 스프링 AOP
    - Mockito
    - 하이버네이트 lazy initialization

# CGLib 

> [CGLib API Docs](http://cglib.sourceforge.net/apidocs/)

CGLib 은 클래스 기반 프록시를 가능하게 해줍니다. 서브 클래스를 만들 수 있는 라이브러리를 사용하여 프록시를 만들 수 있습니다.

- 스프링, 하이버네이트가 사용하는 라이브러리
- 버전 호환성이 좋지 않아서 서로 다른 라이브러리 내부에 내장된 형태로 제공되기도 한다.

```java
public class BookServiceTest {
  @Test
  public void di() {
    MethodInterceptor handler = new MethodInterceptor() {
      UserService userService = new UserService();
      @Override
      public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        return method.invoke(bookService, args);
      }
    };
    // Enhancer 클래스는 CGlib 의 핵심적인 클래스
    // CGLib은 Enhancer라는 클래스를 통해 Proxy를 생성할 수 있다.
    UserService userService = (UserService) Enhancer.create(UserService.class, handler);
  }
}
```

- 서브클래스를 만드는 방법의 단점
    - 상속을 사용하지 못하는 경우 프록시를 만들 수 없다.
    - private 생성자만 있는 경우
    - Final 클래스인 경우
    - 인터페이스가 있을 때는 인터페이스의 프록시를 만들어 사용할 것

## CGLIB InvocationHandler Inteface

```java
package org.springframework.cglib.proxy;

import java.lang.reflect.Method;

public interface InvocationHandler extends Callback {
    Object invoke(Object var1, Method var2, Object[] var3) throws Throwable;
}
```

## 성능차이

CGLib 과 JDK dynamic proxy 의 invoke 메서드 성능 차이는 CGLib 이 성능상 더 우세합니다. 그 이유는 CGLib은 타깃에 대한 정보를 제공 받기 때문입니다.

따라서 CGLib 은 제공받은 타깃 클래스에 대한 바이트 코드를 조작하여 Proxy 를 생성하기 때문에, Handler안에서 타깃의 메소드를 호출할 때 다음과 같은 코드가 형성됩니다.

```java
public Object invoke(Object proxy, Method proxyMethod, Object[] args) throws Throwable {
  Method targetMethod = target.getClass().getMethod(proxyMethod.getName(), proxyMethod.getParameterTypes());
  Ojbect retVal = targetMethod.invoke(target, args);
  return retVal;
}
```

- 메소드가 처음 호출되었을 때 동적으로 타깃의 클래스의 바이트 코드를 조작
- 이후 호출시엔 조작된 바이트 코드를 재사용

기존의 CGLib 은 권장되지 않았는데 그 이유는 다음과 같습니다.

- net.sf.cglib.proxy.Enhancer 의존성 추가
- default 생성자
- 타깃의 생성자 두 번 호출

하지만 어느 시점부터 Spring Boot에선 CGLib을 방식으로 Proxy를 생성해주고 있었는데 이에 대해 어떤 한 개발자가 다음과 같은 의문을 제기했습니다.

__인터페이스를 구현한 클래스가 왜 CGLib방식으로 Proxy를 생성하고 있냐 ?__

[Spring Boot의 리더이자 총책임자인 Phil 의 답변을 토대로 보면](https://github.com/spring-projects/spring-boot/issues/8434) 결과적으로 기존의 CGLib 이 가지고 있던 대부분의 한계들이 개선이 되어, Spring Boot 에선 성능이 좋은 CGLib 을 기본으로 Proxy 를 생성하게 되었습니다. 

# 구현체를 의존관계 주입받을 때 발생하는 에러

사실 이 포스트를 올리게 된 계기가 바로 __구현체를 의존성 주입받는 경우 에러가 발생__ 하여 이 문제를 해결하고자 파고파고 들다보니 JDK dynamic proxy 와 CGLib 까지 오게되었다.

문제 원인은 다음과 같았다.

- 인터페이스
    - TestService
- 구현체
    - TestServiceImpl
- 클라이언트
    - UserServiceImpl

```java
@RequiredArgsConstructor
@Service
public class UserServiceImpl implements UserService {

    private final TestServiceImpl testService;

    // 생략

}
```

회사 코드를 보다가 다음과 같이 구현체로 의존관계를 받고있었는데 서버 기동을 하니 아래와 같은 에러가 발생하였다.

```
bean 'testServiceImpl' could not be injected as a 'net.weave.test.service.TestServiceImpl' 
because it is a JDK dynamic proxy that implements:
net.weave.test.service.TestService
Action:
Consider injecting the bean as one of its interfaces or forcing the use of CGLib-based proxies by setting proxyTargetClass=true on @EnableAsync and/or @EnableCaching.
```

그리고 이 문제를 해결하기위해 참고한 블로그는 다음과 같다. 

> [gmoon92 jdk-dynamic-proxy-and-cglib](https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html)

그리고 내가 테스트한 방식은 다음과같고, 테스트 결과에 의문이 생겨 우아한 형제들에 계시는 김영한님이 강의하시는 인프런 스프링 핵심원리도 들었겠다, 질문/답변란에 다음과 같이 질문을 하였다.

```
// 일부 생략

테스트 방식을 AnnotationConfigApplicationContext 를 사용하여 다음과 같이 했습니다.

1. @Configuration 을 적용한 TestConfig 의 bean.getClass 확인 
결과 : class net.mayeye.site.config.TestConfig$$EnhancerBySpringCGLIB$$ff8bbd9f

2. TestService 인터페이스를 구현한 TestServiceImpl 의 bean.getClass 확인
결과 : class net.mayeye.site.module.test.TestServiceImpl

3. 인터페이스를 구현하지 않고 @Service 애노테이션을 사용하여 빈으로 등록된 TestServiceImpl 의 bean.getClass 확인
결과 : class net.mayeye.site.module.test.TestServiceImpl

여기서 궁금한게 위 블로그에 나온 설명대로라면 인터페이스가 존재하지 않는 경우 세 번째 결과에서 CGLib 을 사용했을거같은데 그러면 결과가 @Configuration 을 사용한 경우와 같이 뒤에 CGLIB 이 붙어야한다고 생각하는데, 제가 생각한 결과랑 다르게 나와서 이렇게 질문을 남깁니다.

@Configuration 을 사용하는 경우에만 빈으로 등록될때 CGLib 을 사용하고 @Service, @Controller 와 같은 애노테이션의 경우에는 빈으로 등록될때 JDK dynamic 방식을 사용하는건가요?
```

- 답변

```
안녕하세요. 정호님
사실 테스트가 잘못되었습니다.
JDK dynamic이나 CGLib는 AOP 또는 @Configuration처럼 특별한 동작을 해서 스프링이 동적 코드 생성이 필요할 때만 사용합니다.
따라서 @Service, @Controller 같은 경우에는 proxy 기술을 사용하지 않고, 스프링이 해당 클래스를 그대로 사용합니다.
AOP를 적용한 다음에 결과를 출력해보시면 원하는 결과를 얻으실 수 있을거에요.
감사합니다.
```

@Service 나 @Controller 같은 애노테이션의 경우 proxy 기술을 사용하지 않는것이었다.. 프록시 기술은 JDK dynamic 이나 CGLib 은 AOP 또는 @Configuration 처럼 특별한 동작을 해서 `스프링이 동적 코드 생성이 필요할 때만 사용`한다고 친절하게 답변해주셨다.

__즉, @Service 나 @Controller 애노테이션을 이용한 빈 등록(Component Scan 방법)은 동적으로 코드 생성이 필요한 경우가 아니라는 것이다.__ 

아직 풀리지 않은 궁금증이 있는데 스프링 부트가 CGLib 을 기본 Proxy 로 사용한다면, 위 질문에 적힌 오류 내용을 보면 

```
bean 'testServiceImpl' could not be injected as a 'net.weave.test.service.TestServiceImpl' 
because it is a JDK dynamic proxy that implements:
net.weave.test.service.TestService
Action:
Consider injecting the bean as one of its interfaces or forcing the use of CGLib-based proxies by setting proxyTargetClass=true on @EnableAsync and/or @EnableCaching.
```

인터페이스를 주입받거나 CGLib-based proxies 를 사용하게끔 설정하라는거같은데... 이 부분에 대해서는 테스트를 더 하고 다음 포스트에서 작성해야겠다.

한가지 의심되는 부분은 위 블로그 내용 중 다음과 같은 글이 있다.

> Spring은 AOP Proxy를 생성하는 과정에서 자체 검증 로직을 통해 타깃의 인터페이스 유무를 판단합니다.
>
> 이때 만약 타깃이 하나 이상의 인터페이스를 구현하고 있는 클래스라면 JDK Dynamic Proxy 의 방식으로 생성되고 인터페이스를 구현하지 않은 클래스라면 CGLIB의 방식으로 AOP 프록시를 생성해줍니다.

위 내용과 김영한님의 답변 내용을 종합해보자면 __DI(Dependency Injection)는 동적 코드 생성이 필요한 경우이고 따라서 Proxy 를 사용하는데 인터페이스를 구현한 구현체의 경우 JDK dynamic proxy 를 사용하고 인터페이스가 없고 클래스만 있는 경우에는 CGLib 을 사용__ 이렇게 생각할 수 있을거 같다.

다음 포스트에서는 다른 테스트 방법과 스프링 AOP 에 대해서 더 다뤄볼 생각이다.

# 참고

> [인프런. 스프링 핵심 원리](https://www.inflearn.com/questions/118048)
>
> [인프런. 더 자바 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation)
>
> https://jaehun2841.github.io/2018/07/21/2018-07-21-spring-aop3/#jdk-dynamic-proxy
>
> https://www.baeldung.com/cglib
>
> https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html
>
> [Head First Design Pattern]()
> 
> https://tram-devlog.tistory.com/entry/Spring-AOP-weaving-proxy
