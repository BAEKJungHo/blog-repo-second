---
title: "build.gradle 에 대한 설명"
layout: post
category: Java
tags: [Java]
excerpt: "build.gradle 에 대한 설명 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

build.gradle 에 대해 간단하게 알아보겠습니다.

# build.gradle

```java
plugins {
    // org.springframework.boot 의 version 만 올리면 다른 라이브러리들에 대한 버전까지 관리해준다.
    // Spring Boot Gradle Plugin 래퍼런스에서는 io.spring.dependency-management 까지 사용하기를 권장하고 있다.
    // io.spring.dependency-management 를 사용하면 2.4.3 스프링 부트 버전에 해당하는 라이브러리의 버전을 자동으로 가져와서 추가한다.
    // 즉, 라이브러리끼리의 충돌 가능성이 줄어든다.
    id 'org.springframework.boot' version '2.4.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

// war 파일 생성 시 artifact-version 이름으로 생성된다.
group = 'jpabook'
version = '0.0.1-SNAPSHOT' // SNAPSHOT 은 아직 개발 단계임을 의미한다.
sourceCompatibility = '11' // 자바 파일을 컴파일 하는데 사용되는 Java 버전
// sourceCompatibility vs targetCompatibility
// https://stackoverflow.com/questions/16654951/gradle-sourcecompatibility-vs-targetcompatibility

// 컴파일 타임에 바이트 코드를 수정하는 Lombok 같은 프로세서를 설정한다.
// https://reflectoring.io/maven-scopes-gradle-configurations/
configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

// Maven Central repository 에서 라이브러리를 가져올 수 있다.
// dependencies 에 가져올 라이브러리를 지정한다.
repositories {
    mavenCentral()
}

// Maven Central repository 에서 가져올 라이브러리
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'

    // JUnit4 추가
    testImplementation("org.junit.vintage:junit-vintage-engine") {
        exclude group: "org.hamcrest", module: "hamcrest-core"
    }
}

// JUnit 5 부터 모듈이 3개로 나뉘어 진다.
    // JUnit Platform : 테스트 프레임워크가 동작하도록 TestEngine API 를 정의하는 모듈
    // JUnit Jupiter : JUnit 5 에서 지원하는 새로운 기능들을 위한 모듈
    // JUnit Vintage : JUnit 5 플랫폼에서 JUnit 3 나 JUnit 4 기반의 테스트를 지원하기 위한 모듈
// Gradle 4.6 버전 이상에선 JUnit Platform 테스트를 실행하기 위해 native support 를 제공한다.
// 이를 활성화하기 위해선 build.gradle 의 test 블록에서 useJUnitPlatform() 메서드를 호출하면 된다.
test {
    useJUnitPlatform()
}
```

## 참고

> [maven-scopes-gradle](https://reflectoring.io/maven-scopes-gradle-configurations/)
>
> [stackoverflow](https://stackoverflow.com/questions/16654951/gradle-sourcecompatibility-vs-targetcompatibility)