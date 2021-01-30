---
title: "노티피케이션(notification) 패턴"
layout: post
category: DesignPattern
tags: [DesignPattern]
excerpt: 노티피케이션(notification) 패턴 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/DesignPattern.jpg)

# 서론

노티피케이션(notification) 패턴에 대해서 배워보겠습니다.

# 노티피케이션(notification) 패턴

노티피케이션(notification) 패턴은 너무 많은 `UncheckedException(미확인 예외)`를 사용하는 상황에 적합한 해결책을 제공한다.
이 패턴에서는 도메인 클래스로 오류를 수집한다.

- 오류를 수집하는 도메인 클래스(Notification)

```java
public class Notification {

    private final List<String> errors = new ArrayList<>();

    public void addError(final String message) {
        erros.add(message);
    }

    public boolean hasErrors() {
        return !errors.isEmpty();
    }

    public String errorMessage() {
        return errors.toString();
    }

    public List<String> getErrors() {
        return this.errors;
    }

    // 노티피케이션 패턴(Notification Pattern)
    public Notification validate() {
        final Notification notification = new Notification();
        if(this.description.length() > 100) {
            notification.addError("The description is too long");
        }

        final LocalDate localDate;
        try {
            parsedDate = LocalDate.parse(this.date);
            if(parsedDate.isAfter(LocalDate.now())) {
                notification.addError("date cannot be in the future");
            }
        } catch(DateTimeParseException e) {
            notification.addError("Invalid format for date");
        }

        final double amount;
        try {
            amount = Double.parseDouble(this.amount);
        } catch(NumberFormatException e) {
            notification.addError("Invalid format for amount");
        }

        return notification;
    }   

}
```

## 예외 사용 가이드라인

일반적으로 프로그램 개발에서 예외를 사용하는 가이드라인에 대해서 배워보겠습니다.

### 예외를 무시하지 않음

문제의 근본 원인을 알 수 없다고 예외를 무시하면 안된다. 예외를 처리할 방법이 명확하지 않으면 UncheckedException 을 던지면 된다.

### 일반적인 예외는 잡지 않음

가능한 구체적으로 예외를 잡으면 가독성이 더 높아지고 세밀하게 예외를 처리할 수 있다.

### 에외 문서화

`@throws` 자바독(Javadoc) 문법으로 예외를 문서화할 수 있다.

```java
@throws NoSuchFileException 파일이 존재하지 않을 때
@throws DirectoryNotEmptyException 파일이 디렉터리이고 비어 있지 않아 삭제할 수 없을 때
@throws IOException I/O 오류가 발생했을 때
...
```

### 특정 구현에 종속된 예외를 피할 것

예를들어 어떤 메서드에서 `MySqlExeption `을 던지면 이를 사용하는 코드도 Mysql 에 종속되기 때문에 이러한 코드는 피해야한다.

### 예외로 흐흠 제어하지 않기

예외로 흐름을 제어하는 경우는 안티 패턴이다.

```java
try {
    while(true) {
        System.out.println(source.read());
    }
} catch(NoDataException e) {

} 
```

이러한 코드는 예외를 처리하기 위해 불필요한 try-catch 문이 추가되어 가독성을 떨어뜨린다. 또한 코드의 의도를 파악하기 어렵다.

### null 반환하지 않기

예외대신 null 을 반환하면 해당 기능을 사용하는 클라이언트 코드에서는 항상 null 체크를 해야한다. 또한 `NPE(NullPointerException)` 발생 가능성이 높아진다.


## 참고

> [Real-world Software Development](#)