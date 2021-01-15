---
title: "[객체지향 3대 요소] 다형성(Polymorphism)"
layout: post
category: OOP
tags: [OOP]
excerpt: "객체지향 3대 요소인 다형성에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

# 서론

객체지향 3대 요소인 다형성(Polymorphism)에 대해서 배워 보겠습니다. 다형성은 `객체지향의 꽃`이라 불릴만큼 가장 중요한
요소입니다. 따라서 다형성을 심도있게 공부해야합니다.

아래에 나와있는 예제들은 스프링과 관련이 있습니다. 따라서 아직 스프링이 뭔지 잘 모르시는 분들은 다형성에 대해서만 읽고 글에 나와있는 예제들은 눈으로만 가볍게 봐주시기 바랍니다.

또한 다형성의 고급 활용(스킬)이라 할 수 있는 디자인 패턴에 대해서도 배워 볼 수 있습니다.

# 좋은 객체지향 프로그래밍

전 파트 [객체지향 프로그래밍 OOP](https://baekjungho.github.io/what-is-oop/)에서 OOP 가 뭔지에 대해서 배웠습니다.

이번에는 다형성을 배우기 전에 조금 더 심도 있게 OOP 에 대해 배워보고 다형성을 다루도록 하겠습니다.

OOP 의 핵심은 다음과 같습니다.

> 실세계의 모든 것을 `객체`로 표현할 수 있고 각 객체는 고유의 `역할`, `책임`이 있으며, 객체끼리는 `협력`을 통해 데이터를 처리할 수 있다.

여기서 가장 중요한 key point 는 `역할, 책임, 협력`입니다.

## 역할, 책임, 협력

### 커피 주문

예를들어 카페에서의 주문을 생각해보면 다음과 같이 주문이 이루어집니다.

손님이 커피를 주문한다. 캐시어는 손님의 주문을 받고 결제를 한다. 바리스타는 손님의 커피를 만들어 캐시어에게 전달한다.
캐시어는 손님에게 커피를 서빙한다.

여기서 역할, 책임 협력은 다음과 같습니다.

- `역할`
    - 손님
    - 캐시어
    - 바리스타
- `책임`
    - 손님 : 커피 주문 
    - 캐시어 : 주문 받아서 결제 및 서빙
    - 바리스타 : 커피 제작
- `협력`
    - 커피 주문이라는 행위

손님이 정우성이고 캐시어가 박효신이고 바리스타가 조승우라고 할때, 정우성, 박효신, 조승우는 역할의 `구현체`입니다. 
손님은 캐시어라는 `역할에 의존하지` 박효신이라는 `구현체에 의존하지 않습니다.` 즉, 캐시어가 박효신이 되든 신용재가 되든
누가 되도 손님에게 영향을 끼치지 않습니다. 바로 이것이 다형성이 가진 가장 큰 특징입니다.

> [특징 1] 다형성은 역할에 의존해야지 구현체에 의존하면 안된다. 이로서 클라이언트 코드에 영향을 주지 않고 구현체를 변경할 수 있다.

역할에 의존하게되면 얻게되는 장점은 무엇일까요?

바로 `클라이언트 코드에 영향을 주지 않고, 구현체를 실행 시점에서 바꿀 수 있다는 것`입니다.

구현체를 실행 시점에 바꿀 수 있다는 것은 예를들어 박효신이 알바를 그만두고 신용재가 들어오는 행위를 의미합니다.

### 웹 프로젝트 개발

두 번째 예시로 웹 개발을 들어보겠습니다.

웹 프로젝트를 개발하기 위해서 기획자는 기획 및 화면 설계를 그리며, 디자이너는 기획이 끝나면 기획자와 협의하며 화면을 디자인합니다. 디자인이 끝난 화면을 퍼블리셔는 마크업하며, 퍼블리셔가 끝난 화면을 개발자가 받아서 프로그램 개발을 하게됩니다.

여러분도 같이 손으로 or 머릿속으로 `역할, 책임, 협력`을 그려보시기 바랍니다.

- `역할`
    - 기획자
        - 구현체 : 정우성
    - 디자이너
        - 구현체 : 조보아
    - 퍼블리셔
        - 구현체 : 박효신
    - 개발자
        - 구현체 : 신용재
- `책임`
    - 기획자 : 기획 및 화면설계
    - 디자이너 : 화면 디자인
    - 퍼블리셔 : 마크업
    - 개발자 : 프로그램 개발
- `협력`
    - 웹 프로젝트 개발

우리는 기획자가 이병헌으로 바뀌어도 디자이너, 퍼블리셔, 개발자에게 영향을 끼치지 않습니다. 또한 디자이너가 신세경으로 바뀌어도 기획자, 퍼블리셔, 개발자에게 영향을 끼치지 않습니다.

즉, 우리는 웹 프로젝트 개발할 때 `역할에 의존하지 구현체에 의존하지 않습니다.`

__이렇게 실세계를 개발로 구현하게되는 경우 `역할`과 `구현`을 명확하게 해야하며, 객체 설계 시 역할을 먼저 부여하고 그 다음 역할을 수행하는 구현을 만들면 됩니다.__

### 협력

협력은 `요청`과 `응답`으로 이루어집니다. 요청을 `클라이언트(Client)`라고하며 응답을 `서버(Server)` 라고 생각하면 됩니다.
클라이언트는 또한 서버가 될 수 있습니다.

> 기획자 - 디자이너 - 퍼블리셔 - 개발자

이렇게 서로 협력 관계에서 요청하는 입장에 있는 대상이 클라이언트가 되며, 응답하는 입장에 있는 대상이 서버가 됩니다.

즉, 요청과 응답은 `연쇄적`으로 전달됩니다.

### 역할(Role), 책임(Responsibility), 구현(Implementation)

위 예시를 보면 알 수 있듯이 `역할(Role)`은 `책임(Responsibility)`을 암시합니다. 커피주문에서 손님, 캐시어, 바리스타 역할만 보고서도 책임을 유추할 수 있습니다. `구현(Implementation)`은 역할을 수행하는 사람, 물체 등이라고 생각하면됩니다. 프로그래밍 측면에서는 구현체라고 생각하면됩니다.

위에서 배운 예시를 통해 다음과 같은 특징을 찾아 낼 수 있습니다.

- `여러 사람이 동일한 역할을 수행할 수 있다.`
    - 퍼블리셔가 A 이든 , B 이든, C 이든 개발자 입장에서 크게 중요하지 않다. 퍼블리셔가 그만둔다면 다른 사람을 고용하면된다. 개발자 입장에서는 마크업된 View 파일을 받기만 하면 된다.
- `역할은 대체 가능성을 의미한다.`
    - 개발자 입장에서 퍼블리셔는 대체 가능(Substituable) 하다. 두 명이 동일한 역할만 수행할 수 있다면, 누가 하든 상관없다.
    - 추상화된 것에 의존해야지 구현체에 의존하면 안된다. 예를들어 자동차 역할과 K7, 벤츠, 아우디 등 다양한 구현체가 있다고할 때 구현체가 바뀐다고해서 운전자(클라이언트)에게 영향을 주면 안된다. 즉, 운전자는 자동차(역할)에 의존해야지 구현체에 의존하면 안된다.
- `책임을 수행하는 방법은 자율적으로 선택할 수 있다.`
    - 요청을 받은 사람은 요청을 처리하는 방법을 자유롭게할 수 있다. 개발자가 퍼블리셔에게 마크업된 파일을 요청하면 퍼블리셔는 자신만의 방법과 노하우로 디자이너로부터 디자인 화면을 받아서 마크업해서 개발자에게 전달한다. 퍼블리셔가 누구냐에 따라 일하는 방식(요청을 처리하는 방식)이 다르지만 역할은 동일하다. `즉, 동일한 요청에 대해서 역할을 수행하는 사람들마다(구현체) 서로 다른 방식으로 응답할 수 있는 능력을 다형성(Polymorphism)` 이라고 한다.
- `한 사람이 동시에 여러 역할을 수행할 수 있다.`
    - 예를들어 개발자의 역할을 수행하고 있는 A 씨는 퍼블리셔 경험 1년, 디자이너 경험 1년이 있다고하면, A 씨는 퍼블리셔와 디자이너의 역할 또한 수행 할 수 있을 것이다.

> [객체지향과 사실의 오해](). 객체지향 설계라는 예술은 적절한 객체에게 적절한 책임을 할당하는 것에서 시작된다. 책임은 객체지향 설계의 품질을 결정하는 가장 중요한 요소다. 책임이 불분명한 객체는 애플리케이션의 미래 역시 불분명하게 만든다. 역할은 유연하고 재사용 가능한 협력 관계를 구축하는 데 중요한 설계 요소다. 

위에서 다형성의 두 번째 특징이 나왔습니다.

> [특징 2] 동일한 요청에 대해서 서로 다른 방식으로 응답할 수 있다.

### 메시지(Message)

객체지향에서는 객체들이 의사소통하는 것을 `메시지(Message)`라고 합니다. 한 객체가 다른 객체에게 요청하는 것을 메시지 전송이라고 하고 받는 것을 메시지 수신이라고 합니다.

메시지 수신을 처리하는 방법을 `메서드(Method)`라고 합니다.

객체는 `상태(state)`와 `행위(behavior)`로 이루어져 있습니다.

### OOP 의 핵심

위에서 배운 내용을 통해 OOP 의 핵심을 정의하자면 다음과 같습니다.

__객체지향 프로그래밍의 핵심은 클래스가 아니고 객체들간의 협력과 적절한 책임을 할당하는데에 있습니다.__

# 다형성(Polymorphism)

위에서 배운 다형성의 특징을 정리해보겠습니다.

- 특징
    - 다형성은 역할에 의존해야지 구현체에 의존하면 안된다. 이로서 클라이언트 코드에 영향을 주지 않고 구현체를 변경할 수 있다.
    - 동일한 요청에 대해서 서로 다른 방식으로 응답할 수 있다.

그러면 이번에는 해당 특징을 실제로 코드로 만들어 이해하는 시간을 가져보겠습니다. 다형성이 실제로 실무에 어떻게 적용되는지 배울 수 있습니다.

## 클라이언트 코드에 영향을 주지않고 서버 코드를 변경하는 방법

`좋은 객체지향 프로그래밍`에서 배운 `역할, 책임, 협력`의 관점에서 요구사항을 보고 예제를 만들어 보겠습니다.

요구사항 내용은 다음과 같습니다. 실제로 회사에서 제가 직접 경험한 프로젝트이며 다형성을 적용한 사례입니다. 예제를 보기전에 아래 요구사항을 읽고 어떤 식으로 개발하면 좋을지 혼자서 생각해보는 시간을 갖는것을 추천드립니다.

### 더보기 모듈 개발

```
유튜브와 같은 동영상 플랫폼을 개발하려고한다. 사용자 메뉴는 인기영상, 최신영상, 구독영상으로 총 3개로 구분되어있으며
각 목록페이지에 페이징 대신 하단에 더보기 버튼을 만들어 더보기 버튼 클릭 시, 추가로 데이터를 불러오게 하고싶다.
```

위에서 배운 것 처럼 우리는 `역할`을 가장 먼저 뽑아내야 합니다. 그리고 `구현`을 뽑아내면 됩니다.

- 역할
    - 더보기 조회
- 구현
    - 인기영상 더보기 조회
    - 최신영상 더보기 조회
    - 구독영상 더보기 조회
- 책임
    - 더보기 클릭 시 추가 데이터 가져오기
- 협력
    - 영상 목록 화면에 더보기 데이터 출력


먼저 구현체에 의존하고 있는 코드를 보겠습니다.

```java

public class SeeMoreService {

    // 인기영상 Video Service 구현체를 의존 하고있다.
    private VideoService videoService = new HotVideoService();

    public List<VideoVo> findMoreVideo(VideoVo videoVo) {
        return videoService.findAllVideo(VideoVo);
    }

}
```

만약에 인기영상이 아니라 최신영상과 구독영상 대한 더보기가 필요한 경우 구현체를 더 주입받을 것인가 ?

```java
// 인기영상 Video Service 구현체를 의존 하고있다.
private VideoService hotVideoService = new HotVideoService();
// 최신영상 Video Service 구현체를 의존 하고있다.
private VideoService latestVideoService = new LatestVideoService();
// 구독영상 Video Service 구현체를 의존 하고있다.
private VideoService subscriptionVideoService = new SubscriptionVideoService();
```

그러면 SeeMoreService 에서 3개의 구현체에 의존하는 코드를 만들게 되며 메서드도 3개나 될 것입니다.

만약에 나중에 고객이 좋아요영상, 싫어요영상, 나중에볼영상, 이달의 영상 등 여러 요구사항(목록페이지)가 추가될때마다 마냥 위처럼 코드를 늘릴순없습니다.

위에서 `클라이언트(요청)`는 SeeMoreService 가 되며 `서버(응답)`는 VideoService 가 됩니다. 다형성의 특징 1번째에서 우리는 클라이언트에게 영향을 주지않고 서버의 코드를 바꿀 수 있어야 한다고 배웠습니다.

하지만 지금 위 코드는 서버 코드를 바꾸기 위해서는 클라이언트 SeeMoreService 에서 구현체를 직접 변경해줘야하기 때문에 클라이언트에도 변경이 일어납니다. (다형성 부적합)

> 이렇게 코딩을 해도 만약 회사내에 객체지향에 관심 없고 오직 그냥 프로그램이 동작만 하면된다라는 마인드를 가진 사람이 많으면 본인이 경험이 낮아 이렇게 코딩을 했어도 오히려 잘했다고하고 더 나은 방향을 제시해줄 사람이 아무도 없을 수 있다.

자!! 우리는 다형성을 공부했으니 실제로 다형성을 적용해봅시다.

__다형성을 적용하기 위해, 다형성을 극대화하는 `디자인 패턴`이라는 것을 적용해서 위 문제를 해결할 것입니다.__

사실 SI 에 정형화된 사실 SI 뿐만아니라 웹 개발에 정형화된 `인터페이스-구현체 패턴`만 익숙해지고 객체지향과 다형성에 대해 고민없이 코딩한다면 분명 위처럼 코딩했을것입니다. 

즉, HotVideoService 에는 ex) 인기영상조회, 인기영상등록 등 여러 책임을 수행하고 있을 가능성이 큽니다.

더 나은 방향으로 역할과 책임을 분리한다면 다음과 같이 할 수 있습니다.

- 영상 등록, 수정, 삭제를 담당하는 역할
- 영상 조회를 담당하는 역할

우리는 더보기 요구사항을 만들어야 하며 더보기는 `영상 조회`와 관련이 있다. 따라서 최신영상조회, 인기영상조회, 구독영상조회 등 `영상 조회에 관한 역할`을 다음과 같이 만들 것입니다.

사실 SI 에 정형화된 패턴으로 `인터페이스와 구현체를 1:1`로 두어서 할 수도 있지만, 조회(Find) 라는 구현체가 다양하게 생기지 않을 가능성이 크므로 인터페이스 없이 클래스로 만들어 사용하겠습니다.

```java
@Transactional(reandOnly = true)
@RequiredArgsConsturctor
@Service
public class VideoFindService {

    private final VideoRepository videoRepository;

    public List<VideoVo> findAllHotVideo() {
        // 생략
    }

    public List<VideoVo> findAllLatestVideo() {
        // 생략
    }

    public List<VideoVo> findAllSubscriptionVideo() {
        // 생략
    }

}
```

인기영상, 최신영상, 구독영상 각 조회는 쿼리가 각각 다르므로 메서드를 찢어서 만드는것이 좋습니다.

변경된 SeeMoreService 는 다음과 같습니다.

```java
@RequiredArgsConsturctor
@Service
public class SeeMoreService {

    // @RequiredArgsConsturctor 를 사용하면 생성자 의존관계 주입을 해준다.
    // = private VideoFindService videoFindService = new VideoFindService();
    private final VideoFindService videoFindService;

    public List<VideoVo> findMoreVideo(VideoVo videoVo) {
        // 인기영상 더보기 조회
        // 엥... 최신영상과 구독영상은 어떻게 처리하지?...
        return videoService.findAllHotVideo(VideoVo);
    }

}
```

VideoFindService 는 사실 인터페이스가 존재하지 않고 클래스만 있기 때문에 구현체에 의존하게됩니다. 대신 구현체가 더 생성될 가능성은 아주 희박합니다. 

findMoreVideo 에서 최신영상과 구독영상, 인기영상에 따라 각각 알맞은 메서드를 호출해서 처리를 해야 합니다. 이러한 경우에 바로
`전략 패턴(Strategy Pattern)` 을 사용하여 문제를 해결할 수 있습니다.

> 디자인 패턴이란 여러 상황에서 발생하는 문제들에 대한 공통적인 해결방법을 패턴화한 것을 의미합니다.

### 전략패턴으로 리팩터링

전략 패턴은 `전략(Strategy)`이라는 `알고리즘 하나`와 그에 대한 `여러 구현체`들이 존재하는 패턴입니다.

즉, 우리는 더보기라는 전략 알고리즘을 만들 것입니다.

```java
// 더보기 전략 인터페이스
public interface SeeMoreStrategy {

    // 더보기 알고리즘
    List<VideoVo> findMoreVideo(VideoVo videoVo);

}
```

이제 더보기가 필요한 기능마다 해당 구현체를 만들어 줄 것입니다.

- 인기영상 더보기 전략 구현체

```java
@RequiredArgsConstructor
@Service
public class SeeMoreHotVideo implements SeeMoreStrategy {

    private final VideoFindService videoFindService;

    @Override
    public List<VideoVo> findMoreVideo(VideoVo videoVo) {
        return videoFindService.findAllHotVideo(videoVo);
    }
    
}
```

- 최신영상 더보기 전략 구현체

```java
@RequiredArgsConstructor
@Service
public class SeeMoreLatestVideo implements SeeMoreStrategy {

    private final VideoFindService videoFindService;

    @Override
    public List<VideoVo> findMoreVideo(VideoVo videoVo) {
        return videoFindService.findAllLatestVideo(videoVo);
    }
    
}
```

- 구독영상 더보기 전략 구현체

```java
@RequiredArgsConstructor
@Service
public class SeeMoreSubscriptionVideo implements SeeMoreStrategy {

    private final VideoFindService videoFindService;

    @Override
    public List<VideoVo> findMoreVideo(VideoVo videoVo) {
        return videoFindService.findAllSubscriptionVideo(videoVo);
    }
    
}
```

- 더보기 서비스

```java
@RequiredArgsConstructor
@Service
public class SeeMoreService {

    // SeeMoreStrategy 를 구현한 빈들이 Map 에 담긴다.
    // DIP : 구현체에 의존하지 않고 SeeMoreStrategy 라는 인터페이스에 의존하게 되었다. 
    private final Map<String, SeeMoreStrategy> strategyMap;

    // 파라미터가 dto 인 이유는 더보기는 ajax 를 통해 비동기 형식으로 데이터를 가져와야하기 때문에
    // API 요청과 응답을 처리하기 위해서 VO 가 아닌 DTO 타입으로 만들었다.
    public List<VideoVo> findMoreVideo(VideoDto.SeeMoreRequest dto) {
        String strategyKey = dto.getStrategyKey;
        if(!strategyMap.containsKey(strategyKey)) {
            throw new NotFoundStrategyException("더보기 모듈 호출을 위한 전략을 찾지 못했습니다.");
        }
        SeeMoreStrategy strategy = strategyMap.get(strategyKey);

        return strategy.findMoreVideo(dto.toEntity());
    }

}
```

자 이로서 우리는 클라이언트에서 적절한 전략 키값을 가지고 Map 담긴 전략 구현체를 꺼내와서 각 요구사항(인기, 최신, 구독)에 맞게 적합한 조회 메서드를 호출할 수 있게 됬습니다.

따라서 더 많은 요구사항들(좋아요, 싫어요 영상 등)이 생겨도 클라이언트(SeeMoreService)에 영향을 주지않고 서버 기능을 확장할 수 있게 되었습니다.

사실 위 예제를 통해서 `클라이언트 코드에 영향을 주지않고 서버 코드를 변경하는 방법` 외에도 우리는 몇가지 더 배운것이 있습니다.

바로 객체지향 설계원칙의 `SRP, DIP 그리고 OCP` 입니다. 

> 객체지향 설계원칙(SOLID)은 다음 편에서 더 자세하게 다룰 예정입니다.

_SRP, DIP, OCP 가 뭔데 ?!!_

`DIP, OCP` 는 객체지향 설계원칙에 속한 녀석들입니다. 그리고 그 중에서 `가장 중요한` 녀석들입니다. `SRP` 는 DIP, OCP 보다는 덜 중요하지만 그래도 중요합니다.

- `SRP(Single Resposibility Principle, 단일 책임 원칙)`
    - 한 클래스는 하나의 책임만 져야 한다.
    - 변경이 있을때 파급효과가 작으면 해당 원칙을 잘 지켰다고 할 수 있다.
- `DIP(Dependency Inversion Principle, 의존관계 역전 원칙)`
    - 추상화에 의존해야지 구체화에 의존하면 안된다.
    - 즉, 인터페이스에 의존하고 구현체에 의존하지 말라는 것
    - 즉, 역할에 의존해야지 구현에 의존하지 말라는 것
- `OCP(Open/Closed Principle, 개방 폐쇄 원칙)`
    - 확장(새로운 기능 추가)에는 열려 있어야 하며, 변경(기존 코드의 변경)에는 닫혀있어야 한다.

위 코드에서 `SRP` 를 지킨 경우는 바로 VideoFindService 로 리팩터링한 경우입니다. 영상 조회라는 책임을 담당하는 클래스를 만들어 하나의 책임만 지게 했습니다.

`DIP` 를 지킨 경우는 바로 `private final Map<String, SeeMoreStrategy> strategyMap;` 이 부분입니다. SeeMoreService 는 전략 인터페이스에 의존하지 해당 구현체들에 의존하지 않습니다.

`OCP` 를 지킨 경우는 바로 `전략 패턴(Strategy Pattern`을 사용하여 더 많은 요구사항들이 생겨도 클라이언트 코드에 영향을 주지않고(기존 코드의 변경에 닫혀있고) 확장(새로운 기능 추가)할 수 있게 되었습니다.

### 다형성과 인터페이스

위에서 계속 반복되서 나오는 다형성의 특징인 `역할에 의존해야지 구현에 의존하면 안된다`에서 역할은 인터페이스를 의미한다는 것을 배웠습니다. 이렇듯 다형성과 인터페이스는 거의 한 몸, 자웅동체라고 생각하면됩니다.

인터페이스를 두어서 얻는 이점은 세부 구현체를 숨기고 인터페이스를 바라보게 함으로써 클래스 간의 의존관계를 줄이는 것, 결합도를 낮추고, 다형성을 위해서입니다. 즉, `낮은 결합도(decoupling)` 와 `다형성(polymorphism) `때문입니다.

결합도가 낮으면 변경에 용이해지기 때문에 유지보수성을 높일 수 있습니다.

> 스프링을 배우게 되면 DI(Dependency Injection) 원칙이 등장하는데, 이 원칙의 기본 아이디어는 실제 사용할 세부 구현체를 숨기고 추상화된 인터페이스를 통해 간접적으로 접근하는 것입니다. DI 원칙을 따르면 장점이 클래스 간의 의존관계가 줄어들기 때문에 decoupling 할 수 있습니다.

### 스프링과 DI

사실 위 예제가 스프링으로 되어있는데 스프링은 핵심은 `객체지향 설계를 가장 극대화하여 활용할 수 있게 해주는 프레임워크`입니다. 스프링의 여러 핵심 기술 중 가장 중요한걸 꼽으면 바로 `DI(Dependency Injectiopn), 의존 관계 주입` 입니다.

해당 기술을 통해서 `다형성 + OCP, DIP` 를 잘 지켜 프로그래밍 할 수 있게 도와줍니다.

순수 자바로는 OCP, DIP 를 지킬 순 있지만 결국 나중에는 `DI 컨테이너`를 만들게 됩니다.

## 동일한 요청에 대해 서로 다르게 응답하는 방법

이번에는 `동일한 요청에 대해 서로 다르게 응답하는 방법`에 대해서 배워보겠습니다. 위에서 배운 내용보다는 가볍고 자바 기본서에도 자주 등장하는 내용으로 가벼운 마음으로 배우실 수 있습니다.

### 오버로딩(Overloading)

- 특징
    - return Type 과 메서드 명이 동일해야한다.
    - 매개변수의 타입 또는 매개변수의 개수가 달라야한다.

```java
public class User {

    private String userName;
    private int age;
    private String gender;

    public void print(String userName) {
        System.out.println(userName);
    }

    public void print(int age) {
        System.out.println(userName);
    }

    public void print(String userName, String gender) {
        System.out.println(userName + "," + gender);
    }

}
```

즉, 같은 이름의 print 메서드라도 서도 다르게 응답하고 있으며, 오버로딩은 다형성을 구현하는 방법 중 하나입니다.

### 오버라이딩(Overriding)

- 특징
    - 리턴타입, 메서드명, 파라미터 개수, 파라미터 타입이 전부 동일해야한다.
    - 상속, 구현 관계에서 사용할 수 있다.
    - 인터페이스를 구현해서 오버라이딩 하거나, 추상클래스를 상속받아서 메서드를 오버라이딩할 수 있다.

```java
public class Vehicle {

    public void drive() {
        System.out.println("drive");
    }

}
```

```java
public class Car {

    @Override
    public void drive() {
        System.out.println("drive car");
    }

}
```

```java
public class Airplane {

    @Override
    public void drive() {
        System.out.println("drive airplane");
    }

}
```

이처럼 같은 drive 메서드라도 서도 다르게 응답하고 있으며, 오버라이딩은 다형성을 구현하는 방법 중 하나입니다.

## 정리

- 좋은 객체지향 프로그래밍을 하기 위해서는 협력이라는 관점을 생각해야하며, 적절한 역할과 적당항 크기의 책임을 부여해서 개발해야한다.
- 다형성은 객체지향의 꽃이며 다음과 같은 특징을 지니고 있다.
    - 다형성은 역할(인터페이스)에 의존해야지 구현체에 의존하면 안된다. 이로서 클라이언트 코드에 영향을 주지 않고 구현체를 변경할 수 있다.
    - 동일한 요청에 대해 서로 다르게 응답할 수 있다.
    - 다형성이 적용된 객체지향 원칙에는 DIP 와 OCP 가 있다.
- 스프링은 객체지향 프로그래밍을 극대화 시켜주는 프레임워크이며 가장 중요한 기술은 DI 다.
- DI 컨테이너를 통해서 다형성, OCP, DIP 를 지킬 수 있다.
- 다형성을 구현하는 방법 중에는 디자인 패턴이라는 것도 있다.
- 다형성과 인터페이스는 자웅동체이며 인터페이스를 두어서 얻는 이점은 다형성과, 낮은 결합도를 얻을 수 있다.

## 참고

> [객체지향의 사실과 오해](#)
>
> [인프런. 스프링 핵심 원리](#)
>
> [토비의 스프링 3](#)