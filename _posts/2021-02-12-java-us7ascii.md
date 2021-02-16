---
title: "US7ASCII 인코딩의 한글 처리"
layout: post
category: Java
tags: [Java]
excerpt: "자바에서 DB 인코딩이 US7ASCII 인 경우 한글 처리하는 방법에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

DB 에서 charset 이 `US7ASCII` 인 경우 한글 처리 하는 방법에 대해서 배워보겠습니다.

# US7ASCII 의 한글 처리

DB charset 이 `US7ASCII` 인 경우 자바에서는 한글을 DB 에 올바르게 저장하기 위해서 다음과 같이 처리합니다.

```java
public class EncodingConverter {
    
    // US7ASCII > UTF-8
    public static String us7AsciiToUTF8(String str) {
        try {
            if(str != null) {
                return new String(str.getBytes("8859_1"), "EUC_KR");
            }
        } catch(UnsupportedEncodingException e) {
            throw new RuntimeException(Message.UNSUPPORTED_ENCODING);
        }
        return str;
    }

    // UTF-8 > US7ASCII 
    public static String utf8ToUS7ASCII(String str) {
        try {
            if(str != null) {
                return new String(str.getBytes("EUC_KR"), "8859_1");
            }
        } catch(UnsupportedEncodingException e) {
            throw new RuntimeException(Message.UNSUPPORTED_ENCODING);
        }
        return str;
    }

}
```

대부분의 한글은 잘 처리되지만 `뷁, 퉥` 등 과 같은 특이한 한글은 제대로 지원을 하지 않습니다. 

`8859_1` 에서 대응되는 문자가 없는 경우 java char로 `0xfffd` (물음표, ?)로 변환됩니다.

이걸 응용해서 다음과 같은 문제를 해결할 수 있습니다.

예를들어 사용자가 회원가입 시 닉네임을 입력하는데 `고구마퉥퉥` 이라고 입력한 경우 특별한 처리를 해주지 않으면 DB 에는 `고구마??` 로 들어갈 것입니다. 따라서 다음과 같이 처리를 해주어서 특이한 한글을 입력하는 경우를 방지할 수 있습니다.

```java
public static boolean canUseNickname(String str) {
  return EncodingConverter.us7AsciiToUTF8(EncodingConverter.utf8ToUS7ASCII(str))
            .equals(str);
}
```

위 처럼 사용하면 한글 뿐만 아니라 US7ASCII 에서 대응되지 않는 문자들을 거를 수 있습니다.

> 만약, US7ASCII 에서 지원하지 않는 문자도 받고 싶은 경우에는 DB 에 저장할 때 `암호화`하고 DB 에서 읽을 때 `복호화`하여 사용하면 된다.

## 참고

> [8859_1 의 비밀](https://blog.naver.com/anabaral/130043451093)
