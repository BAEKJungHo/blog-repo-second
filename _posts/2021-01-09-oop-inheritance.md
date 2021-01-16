---
title: "[객체지향 3대 요소] 상속(Inheritance)"
layout: post
category: OOP
tags: [OOP]
excerpt: "객체지향 3대 요소인 상속에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

# 서론

객체지향 3대 요소인 상속(Inheritance)에 대해서 배워 보겠습니다.

# 상속(Inheritance)

상속을 배울 때 알아둬야 할 가장 중요한 숙어는 `is a kind of(~의 한 종류)` 입니다.

> 벤츠는 자동차의 한 종류이다.

즉, 상속을 하기 위해서는 위 관계가 성립해야 합니다.

```java
@Geter @Setter
public class Car {
  // 핸들
  protected String handle;
  // 타이어
  protected String tire;
  // 몸체
  protected String body;

  /**
  * 기본 주행 로직
  */
  protected void drive() {
    System.out.println("운전 시작");
  }
}
```

```java
@Getter @Setter
public class Benz extends Car {
  // 자동 주행
  private boolean autoDriving;

  /**
  * 벤츠만의 주행 로직
  */
  @Override
  public void drive() {
      if(autoDriving) {
          System.out.println("start auto driving");
      } else {
          System.out.println("start driving");
      }
  }

}
```

자동차는 핸들, 타이어, 몸체라는 `속성`과 driving() 이라는 `메서드(기능)`를 가지고 있습니다. 그리고 밴츠는 자동차의 기본 속성 + 벤츠만 가지고 있는 속성인 자동 주행과 상위 클래스의 메서드를 오버라이딩하여 `자신만의 로직`을 만들 수 있습니다.

이렇듯 상속을 사용하면 벤츠, 람보르기니, K7 등 여러 자동차의 `공통점(속성 + 기능)`을 뽑아서 상위 클래스로 만들고 하위 클래스에서는 상위 클래스를 상속 받아 자신만의 속성을 정의할 수도 있고, 기능을 오버라이딩하여 자신만의 로직을 만들 수 있습니다.

### 상속의 장점

- 상위 클래스의 기능 중 자신이 수정하고자 원하는 메서드만 골라서 수정하고, 나머지는 상위 클래스에 정의된 기능을 그대로 사용할 수 있다.

### 상속의 단점

- 상속은 컴파일 시에 성격이 결정되기 때문에, 런타임 시에 상속 받은 상위 클래스의 기능을 변경할 수 없다.
- 상위 클래스가 변경되면 하위 클래스도 변경된다.
- 이러한 종속성은 유연성과 재사용성을 떨어뜨린다.
  - 해결방법 : 상위 클래스를 추상 클래스로 만들고 이를 상속받는다. 상위 클래스를 추상 클래스로 둔다는 의미는, 상위 클래스는 기능에 대한 메서드 시그니처만 서술하고 이에 대한 구현은 전부 하위 클래스가 담당하게 하기 위함이다.
  구현을 변경해야하는 경우에 하위 클래스에 정의된 구현을 변경하면 된다.

### 상위 클래스를 추상 클래스로 만들어 상속 받기

```java
public abstract class Car {

    protected String handle;
    protected String tire;
    protected String body;

    protected abstract void drive();

}
```

```java
@Getter @Setter
public class Benz extends Car {
  // 자동 주행
  private boolean autoDriving;

  /**
  * 벤츠만의 주행 로직
  */
  @Override
  public void drive() {
      if(autoDriving) {
          System.out.println("start auto driving");
      } else {
          System.out.println("start driving");
      }
  }

}
```

## 합성(Composition)과 위임(Delegation)

상속의 대안으로 합성과 위임이 있습니다.

합성이란 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용하는 방법을 의미합니다.

그리고 합성을 더 강력하게 사용하는 방법이 위임입니다.

- 위임

```java
public class A {
  private B b;
}
```
위임은 A 클래스를 B 클래스의 서브클래스로 만드는 대신 A 클래스는 B 클래스를 자신의 인스턴스 변수로 만들고, B 클래스에 정의된 행동이 필요할 때는 B 클래스에 위임함으로써 B 클래스의 행동을 재사용할 수 있습니다.

그러면 합성과 위임을 예제를 통해서 배워 보겠습니다.

예제의 큰 틀은 다음과 같습니다. 여행을 가기 위해서는 탈것과 나라를 정해야 갈 수 있습니다.

```
- 여행(Travel)
- 탈것(Vehicle)
- 나라(Country)
```

- 탈것을 나타내는 추상 클래스

```java
@Getter
public abstract class Vehicle {

    private String name;

    public Vehicle(String name) {
        this.name = name;
    }

    protected abstract void drive();

}
```

- 탈것을 구현하는 서브 클래스

```java
public class Airplane extends Vehicle {

    public Airplane(String name) {
        super(name);
    }

    @Override
    protected void drive() {
        System.out.println("비행기 운행 로직 실행");
    }

}

public class Car extends Vehicle {

    public Car(String name) {
        super(name);
    }

    @Override
    protected void drive() {
        System.out.println("자동차 주행 로직 실행");
    }

}
```

- 나라를 나타내는 추상 클래스

```java
@Getter
public abstract class Country {

    private String name;

    public Country(String name) {
        this.name = name;
    }

}
```

- 나라를 구현하는 서브 클래스

```java
public class Korea extends Country {

    public Korea(String name) {
        super(name);
    }

}


public class America extends Country {

    public America(String name) {
        super(name);
    }

}
```

- 여행 클래스

```java
@Getter
public class Travel {

    private Vehicle vehicle;
    private Country country;

    public Travel(Vehicle vehicle, Country country) {
        this.vehicle = vehicle;
        this.country = country;
    }

    public void goTravel() {
        System.out.println("나라 : " + country.getName());
        System.out.println("비행기 : " + vehicle.getName());
        vehicle.driving();
    }

}
```

- 클라이언트 코드

```java
public class TravelApplication {

    public static void main(String[] args) {
        // 컴파일 시점이 아닌, 런타임 시점에 동적으로 변경할 수 있다.
        Travel travel = new Travel(new Airplane("아시아나항공"), new America("LA"));
        travel.goTravel();
    }

}
```

### 합성의 장점

- 합성관계는 주로 has-a 관계라고 부른다.
- 합성은 구현에 의존하지 않는다. 합성은 내부에 포함되는 객체의 구현이 아닌 퍼블릭 인터페이스에만 의존한다.
- 객체 내부가 변경되더라도 영향을 최소화할 수 있기 때문에 변경에 코드가 더 안정적이다.
- 합성은 런타임 시점에 성격이 결정되기 때문에 상속보다 변경에 더 유연하다.
- 상속보다 클래스간의 결합도가 낮아 유지보수에 용이하다.

## white box reuse 와 black box reuse

클래스 상속을 서브클래싱 이라고 하며, 서브클래싱에 의한 재사용을 `화이트박스 재사용(white-box reuse)` 이라고 한다. 화이트박스는 내부를 볼 수 있다는 의미에서 나온 말이다. 상속을 받으면 부모 클래스의 내부가 서브클래스에 공개되기 때문에 화이트박스인 셈이다.

객체 합성은 클래스 상속에 대한 대안이다. 다른 객체를 여러 개 붙여서 새로운 기능 혹은 객체를 구성하는 것을 의미한다. 객체를 합성하려면, 합성에 들어가는 객체들의 인터페이스를 명확하게 정의해 두어야 한다. 이런 스타일의 재사용을 `블랙박스 재사용(black-box reuse)` 이라고 한다. 객체의 내부는 공개되지 않고 인터페이스를 통해서만 재사용 되기 때문이다.

## 참고

> [GOF Design Pattern](#)