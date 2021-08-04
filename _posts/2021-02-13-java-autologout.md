---
title: "장기간 미사용으로 인한 로그아웃 구현"
layout: post
category: Java
tags: [Java]
excerpt: "장기간 미사용으로 인한 로그아웃 구현에 대한 정리"
author: BAEKJungHo
---

* content
{:toc}

![logo](/images/posts/logo/JAVA.jpg)

# 서론

홈페이지마다 기본 세션시간에 대부분 10분 ~ 15분 정도 한다.

세션시간이 만료되어 로그아웃되는경우 사용자에게 별도의 메시지를 팝업 형태로 안내 할 수도 있고, 안 할 수도 있다.

세션시간이 만료되어 로그아웃 되어 메시지를 팝업 형태로 뿌릴때 보통 멘트는 다음과 같다.

`장기간 미사용으로 인해 로그아웃 되었습니다.`

이 기능은 다음과 같이 구현하면 된다.

# 장기간 미사용으로 인한 로그아웃 구현

실제로 프로젝트에서 해당 기능을 구현 하기 위해서 내가 생각한 흐름이다.

- 로그인 후 사용자들이 홈페이지에 존재하는 서비스(메뉴)들을 이용하다가 세션 시간이 만료되면
- "장기간 미사용으로 인해 로그아웃 되었습니다." 라는 문구로 팝업을 출려해야한다.
- 해당 문구는 로그인이 필요한 서비스에서만 출력하는 것이 아니라 모든 메뉴에 대해서 세션이 존재하는지 판단해서 출력해야 한다.
- 그러면 인터셉터에서 구현하는 것이 편하겠고 
- 인터셉터에서 세션이 존재하는지 확인하고
- 세션이 존재하지 않으면 사용자들에게 해당 메시지를 팝업 형태로 뿌리면되네 ?
- 그런데 로그인하지 않은 경우에는 애초에 세션이 없으니까 팝업이 무조건 뜨겠네... 
- 그러면 로그인할 때 쿠키를 같이 생성해서 쿠키 시간을 조금 더 준다음에
- 인터셉터에서 세션이 존재하지 않고 쿠키가 존재하는 경우는 한 번이라도 로그인 했다는 의미이니 이 경우에만 메시지를 뿌리면 되겠다.
- 그리고 메시지를 뿌리기 전에 쿠키를 만료 시켜야 한 번만 뿌려지겠구나.

즉, 기존 로그인 시 세션만 생성했던 코드를 아래와 같은 조건을 추가해줬다.

- 로그인 시 : `세션 + 쿠키생성`
    - 쿠키 시간은 3시간(임의로 준것임. 본인이 판단해서 세션시간 보다더 주면 된다)
    - 로그인 후 아무 작업도 안하다가 몇십분 혹은 몇시간 뒤에 자리에 와서 메뉴를 클릭 할 수도 있으므로 쿠키 시간을 더 준것 이다.

```java
public class 인터셉터 {
  
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 로그인 세션 조회
        HttpSession session = request.getSession();
        User loginUser = (User) session.getAttribute(LOGIN_SESSION_NAME);
        
        // 쿠키가 존재하지 않으면 애초에 로그인을 안한 것임
        String cookieValue = NOT_LOGIN; 
        
        // 세션이 비어있으면 쿠키 조회
        if(loginUser == null) {
          Cookie cookie = WebUtils.getCookie(request, LOGIN_COOKIE);
          
          // 쿠키가 존재하면 로그인 했다는 것임
          if(cookie != null) {
                cookieValue = cookie.getValue();
                cookie.setValue(null);
                cookie.setMaxAge(0); // 쿠키 만료시키기
                cookie.setPath("/");
                response.addCookie(cookie);
         }
          
          if(!cookieValue.equals(NOT_LOGIN)) {
              request.setAttribute("forward", "/kor.do");
              throw new SessionTimeoutException(Message.LOGOUT_BY_SESSION_TIMEOUT);
          }
        }
    }

}
```
