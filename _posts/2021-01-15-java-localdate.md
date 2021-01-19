---
title: "자바 8 LocalDate 날짜 다루기"
layout: post
category: Java
tags: [Java]
excerpt: "자바 8 에서 제공하는 LocalDate 와 LocalTime, LocalDateTime 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

자바 8 에서 제공하는 LocalDate 와 LocalTime, LocalDateTime 를 이용한 날짜 다루는 방법에 대해서 배워보겠습니다.

# Date 와 Calender 클래스의 문제점

- 불변 객체가 아니다.
    - 따라서 멀티스레드 환경에서 언제든 문제가 발생할 수 있다.
- Calender 는 월(Month) 값 설계가 잘못되었다.
    - 10월을 나타내는 Calender.OCTOBER 의 값은 9 이다.
- Date 의 경우 클래스 이름이 Date 인데 실제로는 시간까지 다룬다.

이러한 문제들을 JodaTime 이라는 오픈소스를 사용하여 문제를 피했었고, Java 8 에서는 LocalDate 를 통해 해결했습니다.

따라서 자바 8 이상일 경우 날짜 다루는 클래스는 LocalDate 를 무조건 써야 합니다.

## Design flaws include

`java.util.Date` 클래스는 다음과 같은 디자인 결함을 포함하고 있습니다.

파파고를 참고하여 번역했습니다. 번역에 오역이 있을 수 있습니다.

> [All about java.util.Date](https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/)
>
> `Its name is misleading`: it doesn’t represent a Date, it represents an instant in time. So it should be called Instant – as its java.time equivalent is.
>
> Date 는 오해의 소지가 있습니다. Date 클래스는 Date 를 나타내지 않고 시간의 한 순간을 의미합니다. 
> 
> `It’s non-final`: that encourages poor uses of inheritance such as java.sql.Date (which is meant to represent a date, and is also confusing due to having the same short-name)
>
> final 이 아닙니다. java.sql과 같은 상속의 잘못된 사용을 장려합니다.
> 
> `It’s mutable`: date/time types are natural values which are usefully modeled by immutable types. The fact that Date is mutable (e.g. via the setTime method) means diligent developers end up creating defensive copies all over the place.
>
> 불변객체가 아닙니다. Date가 변형 가능(예: setTime method)이라는 사실은 부지런한 개발자들이 모든 곳에 방어적인 복사본을 만들게 된다는 것을 의미합니다.
>
> `It implicitly uses the system-local time zone in many places` – including toString() – which confuses many developers. More on this in the “What’s an instant” section
>
> 여러 장소에서 시스템 로컬 시간대를 암시적으로 사용합니다
>
> `Its month numbering is 0-based`, copied from C. This has led to many, many off-by-one errors.
>
> 월의 넘버링은 0 부터 시작합니다. 즉 10월은 9가 됩니다.
>
> `Its year numbering is 1900-based`, also copied from C. Surely by the time Java came out we had an idea that this was bad for readability?
>
> 년도의 넘버링은 1900 부터 시작합니다.
>
> `Its methods are unclearly named`: getDate() returns the day-of-month, and getDay() returns the day-of-week. How hard would it have been to give those more descriptive names?
>
> 메서드 이름이 불명확합니다. getDate() 의 경우 day-of-month 를 리턴합니다.
>
> `It’s ambiguous about whether or not it supports leap seconds`: “A second is represented by an integer from 0 to 61; the values 60 and 61 occur only for leap seconds and even then only in Java implementations that actually track leap seconds correctly.” I strongly suspect that most developers (including myself) have made plenty of assumptions that the range for getSeconds() is actually in the range 0-59 inclusive.
>
> 1초는 0에서 61까지의 정수로 표현됩니다. 값 60과 61은 도약초 동안만 발생하며 그 후에도 실제로 도약초를 정확하게 추적하는 자바 구현에서만 발생합니다.
>
> `It’s lenient for no obvious reason`: “In all cases, arguments given to methods for these purposes need not fall within the indicated ranges; for example, a date may be specified as January 32 and is interpreted as meaning February 1.” How often is that useful?
>
> 예를 들어, 날짜는 1월 32일로 지정될 수 있고 2월 1일을 의미하는 것으로 해석될 수 있다." 이것이 유용할까요?

# 자바 8 에서 제공하는 Date-Time API

JSR-310 스팩의 구현체를 제공합니다.

## 디자인 철학(Date-time Design Principle)

- Clear
    - null 매개 변수 값으로 Date-Time 메서드를 호출하면 일반적으로 NullPointerException이 발생합니다.
- Fluent
    - Date-Time API 는 유창한 인터페이스를 제공하여 코드를 쉽게 읽을 수 있습니다.
    - LocalDate today = LocalDate.now();
    - LocalDate 급여일 = today.with(TemporalAdjusters.lastDayOfMonth()).minusDays(2);
- Immutable
    - Date-Time API 에있는 대부분의 클래스는 변경 불가능한 객체를 생성합니다. 즉, 객체가 생성 된 후에는 수정할 수 없습니다. 따라서 스레드로부터 안전합니다.
    - 접두사는 대부분 of, from 등입니다. Date-Time API에있는 대부분의 클래스는 변경할 수 없기 때문에 API에는 set 메서드가 포함되지 않습니다 .
        - LocalDate dateOfBirth = LocalDate.of(2012, Month.MAY, 14);
        - LocalDate firstBirthday = dateOfBirth.plusYears(1);
- Extensible
    - Date-Time API는 가능한 한 확장 가능합니다. 예를 들어, 자신의 시간 조정자와 쿼리를 정의하거나 자신 만의 캘린더 시스템을 구축 할 수 있습니다.

## 날짜 시간 패키지

- `java.time`
    - 날짜 및 시간을 나타내는 API의 핵심입니다. 여기에는 날짜, 시간, 결합 된 날짜 및 시간, 시간대, 순간, 기간 및 시계에 대한 클래스가 포함됩니다. 이러한 클래스는 ISO-8601 에 정의 된 달력 시스템을 기반으로하며 변경 불가능하고 스레드로부터 안전합니다.
- `java.time.chrono`
    - 기본 ISO-8601 이외의 캘린더 시스템을 나타내는 API입니다. 자신 만의 달력 시스템을 정의 할 수도 있습니다.
- `java.time.format`
    - 날짜 및 시간 형식화 및 구문 분석을위한 클래스.
- `java.time.temporal`
    - 주로 프레임 워크 및 라이브러리 작성자를위한 확장 API로, 날짜 및 시간 클래스, 쿼리 및 조정 간의 상호 운용이 가능합니다. 필드 (TemporalField 및 ChronoField) 및 단위 (TemporalUnit 및 ChronoUnit)는 이 패키지에 정의되어 있습니다.
- `java.time.zone`
    - 표준 시간대, 표준 시간대 오프셋 및 표준 시간대 규칙을 지원하는 클래스. 표준 시간대로 작업하는 경우 대부분의 개발자는 ZonedDateTime 및 ZoneId 또는 ZoneOffset 만 사용해야합니다 .

## 주요 API

- 기계용 시간(machine time)과 인류용 시간(human time)으로 나눌 수 있다.
- 기계용 시간은 EPOCK(1970년 1월 1일 0시 0분 0초)부터 현재까지의 타임스탬프를 표현한다.
- 인류용 시간은 우리가 흔히 사용하는 연,월,일,시,분,초 등을 표현한다.
- 타임스탬프는 `Instant` 를 사용한다.
- 특정 `날짜(LocalDate), 시간(LocalTime), 일시(LocalDateTime)`를 사용할 수 있다.
- 기간을 표현할 때는 `Duration(시간 기반)`과 `Period(날짜 기반)`를 사용할 수 있다.
- `DateTimeFormatter` 를 사용해서 일시를 특정한 문자열로 포매팅할 수 있다.

Date-Time API는 ISO-8601에 정의 된 달력 시스템을 기본 달력으로 사용합니다. 이 달력은 그레고리력 시스템을 기반으로하며 날짜 및 시간을 나타내는 사실상의 표준으로 전역 적으로 사용됩니다. Date-Time API의 핵심 클래스에는 LocalDateTime , ZonedDateTime 및 OffsetDateTime 과 같은 이름이 있습니다 . 이들 모두는 ISO 달력 시스템을 사용합니다 

날짜-시간 API는 [유니 코드 CLDR (Common Locale Data Repository)](http://cldr.unicode.org/)을 사용 합니다.

## DayOfWeek 및 Month enum

Date-Time API는 요일 및 월을 지정하기위한 열거형을 제공합니다.

- DayOfWeek
    - `1(월요일)에서 7(일요일)`까지입니다. 정의 된 상수(`DayOfWeek.FRIDAY`)를 사용하면 코드를 더 쉽게 읽을 수 있습니다.
        - 결과 : DayOfWeek.THURSDAY = DayOfWeek.MONDAY.plus(3);
- Month
    - `1월(JANUARY) ~ 12월(DECEMBER)
    - Month.values() : 1월 ~ 12월 get enum values
    - getValue() : 1월 = 1, 12월 = 12


## LocalDate

자바 8 에서는 SimpleDateFormat 대신 `DateTimeFormatter` 를 이용하여 원하는 형식으로 날짜를 얻을 수 있습니다.

- DateTimeFormatter 상수
    - ISO_DATE_TIME : 2019-03-22T23:56:36.4
    - ISO_LOCAL_DATE : 2019-03-22
    - ISO_LOCAL_TIME : 23:56:36.4
    - ISO_LOCAL_DATE_TIME : 2019-03-22T23:56:36.4
    - ISO_DATE : 2019-03-22
    - ISO_TIME : 23:56:36.4

### Utils

- 현재 날짜 구하기

```java
public static String getToday() {
        return LocalDate.now().format(DateTimeFormatter.ISO_DATE);
}
```

- 현재 날짜에서 n년, n개월, n일, n시간 뒤

```java
/**
 * 현재 날짜에서 n 시간 뒤
 * @return
 */
public static String getAfterMonth(int hour) {
    return LocalDate.now().plusHours(hour).format(DateTimeFormatter.ISO_DATE);
}

/**
 * 현재 날짜에서 n 일 뒤
 * @return
 */
public static String getAfterMonth(int day) {
    return LocalDate.now().plusDays(day).format(DateTimeFormatter.ISO_DATE);
}

/**
 * 현재 날짜에서 n 개월 뒤
 * @return
 */
public static String getAfterMonth(int month) {
    return LocalDate.now().plusMonths(month).format(DateTimeFormatter.ISO_DATE);
}

/**
 * 현재 날짜에서 n 년 뒤
 * @return
 */
public static String getAfterYear(int year) {
    return LocalDate.now().plusYears(year).format(DateTimeFormatter.ISO_DATE);
}
```

- 다음주의 특정 요일 구하기

```java
public static String getNextDayOfWeek(dayOfWeek) {
    // DayOfWeek.WEDNESDAY
    // LocalDate date = LocalDate.of(2021, Month.JANUARY, 20);
    LocalDate today = LocalDate.now();
    LocalDate nextWed = today.with(TemporalAdjusters.next(dayOfWeek));
    return nextWed.format(DateTimeFormatter.ISO_DATE);
}
```

## YearMonth

The following example uses the `MonthDay.isValidYear` method to determine if February 29 is valid for the year 2010. The call returns false, confirming that 2010 is not a leap year.

```java
YearMonth date = YearMonth.now ();
System.out.printf ("% s : % d % n", date, date.lengthOfMonth()); // 2013-06 : 30

YearMonth date2 = YearMonth.of (2010, Month.FEBRUARY);
System.out.printf ("% s : % d % n", date2, date2.lengthOfMonth()); // 2010-02 : 28

YearMonth date3 = YearMonth.of (2012, Month.FEBRUARY);
System.out.printf ("% s : % d % n", date3, date3.lengthOfMonth()); // 2012-02 : 29
```

## MonthDay

```java
MonthDay date = MonthDay.of(Month.FEBRUARY, 29);
boolean validLeapYear = date.isValidYear(2010);
```

## Year

The Year class represents a year. The following example uses the `Year.isLeap` method to determine if the given year is a leap year. The call returns true, confirming that 2012 is a leap year.

```java
boolean validLeapYear = Year.of(2012).isLeap();
```

## TemporalAdjuster

### Predefined Adjusters

```java
LocalDate date = LocalDate.of(2000, Month.OCTOBER, 15);
DayOfWeek dotw = date.getDayOfWeek();
System.out.printf("%s is on a %s%n", date, dotw); // 2000-10-15 is on a SUNDAY

System.out.printf("first day of Month: %s%n",
                  date.with(TemporalAdjusters.firstDayOfMonth())); // first day of Month: 2000-10-01
System.out.printf("first Monday of Month: %s%n",
                  date.with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY))); // first Monday of Month: 2000-10-02
System.out.printf("last day of Month: %s%n",
                  date.with(TemporalAdjusters.lastDayOfMonth())); // last day of Month: 2000-10-31
System.out.printf("first day of next Month: %s%n",
                  date.with(TemporalAdjusters.firstDayOfNextMonth())); // first day of next Month: 2000-11-01
System.out.printf("first day of next Year: %s%n",
                  date.with(TemporalAdjusters.firstDayOfNextYear())); // first day of next Year: 2001-01-01
System.out.printf("first day of Year: %s%n",
                  date.with(TemporalAdjusters.firstDayOfYear())); // first day of Year: 2000-01-01
```

### Custom Adjusters

```java
/**
 * The adjustInto method accepts a Temporal instance 
 * and returns an adjusted LocalDate. If the passed in
 * parameter is not a LocalDate, then a DateTimeException is thrown.
 */
public Temporal adjustInto(Temporal input) {
    LocalDate date = LocalDate.from(input);
    int day;
    if (date.getDayOfMonth() < 15) {
        day = 15;
    } else {
        day = date.with(TemporalAdjusters.lastDayOfMonth()).getDayOfMonth();
    }
    date = date.withDayOfMonth(day);
    if (date.getDayOfWeek() == DayOfWeek.SATURDAY ||
        date.getDayOfWeek() == DayOfWeek.SUNDAY) {
        date = date.with(TemporalAdjusters.previous(DayOfWeek.FRIDAY));
    }

    return input.with(date);
}
```

## 정리

기존 java.util.Date 가 가진 문제들애 대해서 배워봤고, 자바 8 의 Date-time API 의 디자인 철학에 대해서도 배워 봤습니다.

더 자세한 사항은 오라클 Java datetime 자습서를 이용하면 많은 기능들에 대해서 배울 수 있습니다.

## 참고

> [All about java.util.Date](https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/)
>
> [Oracle Java datetime tutorial](https://docs.oracle.com/javase/tutorial/datetime/overview/index.html)
>
> [인프런. 더 자바 8](#)
