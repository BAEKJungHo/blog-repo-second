---
title: "메이븐(Maven)과 그레이들(Gradle)"
layout: post
category: Java
tags: [Java]
excerpt: "메이븐(Maven)과 그레이들(Gradle) 에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

빌드 도구인 메이븐(Maven)과 그레이들(Gradle)에 대해서 배워보겠습니다.

# 빌드 도구의 장점

빌드 도구를 사용하면 다음과 같은 장점이 있습니다.

- 빌드, 테스트, 배포 등 소프트웨어 개발 생명 주기를 자동화할 수 있도록 도와준다.
- 저수준 설정과 초기화에 들이는 시간을 절약하므로 개발에 집중할 수 있다.
- 공통 빌드 작업을 재사용해 이를 다시 구현할 필요가 없으므로 시간을 절약한다.

# 메이븐(Maven)

메이븐은 자바 커뮤니티에서 가장 유명한 빌드 도구입니다.

메이븐은 유지보수에 도움되는 자파 프로젝트 구조를 제공합니다.

- src/main/java : 자바 클래스를 저장하는 패키지
- src/test/java : 테스트 코드를 저장하는 패키지
- src/main/resources : 정적 자원 파일을 저장하는 패키지
- src/test/resources : 테스트에 사용할 정적 자원을 추가하는 패키지

메이븐 명령어는 다음과 같습니다.

- mvn clean : 빌드하기 전에 기존 빌드에서 생성된 부산물을 정리한다.
- mvn compile : 프로젝트의 소스코드를 컴파일해서 target 에 저장한다.
- mvn test : 컴파일된 소스코드를 테스트한다.
- mvn package : Jar 과 같은 적절한 형식으로 컴파일된 코드를 패키징한다.

# 그레이들(Gradle)

그레이들(Gradle)이 메이븐에 비해 갖는 장점은 다음과 같습니다.

- 그루비, 코틀린 등을 이용해 도메인 특화 언어(DSL)를 적용할 수 있다.
- 커스터마이즈가 쉽다.
- 캐시, 점진적 컴파일 등 빌드 시간을 단축하는 기능이 있다.
- 메이븐은 XML 형식으로 되어있는 반면 그레이들은 좀 더 간결한 형식으로 되어있다.

그레이들의 명령어는 다음과 같습니다.

- gradle clean : 이전 빌드에서 생성된 파일을 정리한다.
- gradle build : 응용프로그램을 패키징한다.
- gradle test : 테스트를 실행한다.
- gradle run : application 플러그인의 mainClassName 으로 지정된 메인 클래스를 실행한다.

## 참고

> [Real-world Software Development](#)
