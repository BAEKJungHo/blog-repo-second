---
title: "실행 어라운드(execute around)"
layout: post
category: DesignPattern
tags: [DesignPattern]
excerpt: 실행 어라운드(execute around) 패턴 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/DesignPattern.jpg)

# 서론

실행 어라운드(execute around) 패턴에 대해서 배워보겠습니다.

# 실행 어라운드(execute around) 패턴

실행 어라운드(execute around) 패턴은 함수형 디자인 패턴에서 자주 사용됩니다. 항상 비슷한 작업을 수행하는 초기화, 정리 코드가 있고 초기화, 정리 코드에서 실행하는 비지니스 로직에 따라
이를 파라미터화하고 싶은 상황을 겪어봤을 것입니다.

예를 들어 다음과 같은 상황에서 실행 어라운드 패턴을 사용합니다.

- 파일
    - 파일을 사용하기 전에 열고, 파일을 사용한 후에 닫는다. 작업에 문제가 생기면 예외를 기록해야 한다. 파라미터화 된 코드로 파일의 내용을 읽거나 파일에 데이터를 기록한다.
- 락
    - 임계 구역(critical section) 이전에 락(lock)을 획득한 다음, 크리티컬 섹션에 다음 락을 해제한다. 파라미터화된 코드가 임계 규역이다.
- DB 연결
    - 초기화 작업에서 DB 를 연결하고 작업을 완료한 후 연결을 닫는다. 데이터베이스 연결 풀을 이용하는 상황이라면, 연결 로직에서 풀의 연결을 가져오도록 만들 수 있어 유용하다.                                                                                         

실행 어라운드 패턴에서는 초기화, 정리코드에서 공통 메서드를 추출해 문제를 해결한다.

```java
<R> R extract(final String sql, final Extractor<R> extractor) {
    try(var stmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
        stmt.clearParameters();
        return extractor.run(stmt);
    } catch(SQLException e) {
        throw new IllegalStateException(e);
    }
}
```

## 참고

> [Real-world Software Development](#)