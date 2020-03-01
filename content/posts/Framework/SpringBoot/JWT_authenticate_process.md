# Spring Security



### 필요개념

* 접근주체(Principal) : 접근 사용자
* 인증(Authenticate) : 접근 주체 확인
* 인가(Authorize) : 접근 주체의 권한 검사





<img src="/Users/hoonoh/Desktop/Summary_arc.jpg" alt="Summary_arc" style="zoom:50%;"/>



스프링의 구조는 필터와 필터된 Authentication객체를 가지고 실질적인 Validation을 하는 Provider로 나뉜다.



### Filter chain

스프링 시큐리티 역시 Filter가 구성이 되어있다.

기본적으로 11개의 Filter로 구성이 되어있고, Filter를 커스터마이징 하여 추가 확장 시킬수 있다.

이때 중요한것은 필터간의 순서가 중요하다.



### Provider

프로바이더는 실질적인 검증을 하는 클래스로 AuthenticationProvider를 구현하고 

AuthenticationManagerBuilder에 커스터마이징하여 등록이 가능하다.

```java

  @Override
  protected void configure(AuthenticationManagerBuilder auth) throws Exception {

    auth.authenticationProvider(authenticationProvider);
    auth.authenticationProvider(jwtAutenticationTokenProvider);
  }

```

