## Authentication

스프링 시큐리티 내부에서 인증 된 사용자가 누구인지 증명 할 때 쓰이는 것. 사용자 인증 정보를 저장하는 토큰의 개념에 가깝다. 인증 시에 사용자의 id, password를 담고 검증을 위해 전달되어 사용된다. 그 구조는 다음과 같다.

- principal : 사용자의 아이디나 User 객체를 저장

- credentials : 사용자의 비밀번호

- authorities : 사용자의 권한목록

- details : 인증에 필요한 부가 정보

- Authenticated : 인증 여부

  인증 이후 인증 결과(user 객체, 권한 정보)를 담고 있으며, SecurityContext에 저장되어 전역에서 참조가 가능하다.

```java
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
```



## 기본 동작의 흐름

1. 사용자가 인증 요청(username, password)을 한다.
2. UsernamePasswordAuhenticationFilter가 요청 정보를 바탕으로 Authentication 인터페이스를 구현한 객체를 생성한다. 그 뒤 해당 객체에 사용자 정보를 저장한다.
3. AuthenticationManager가 해당 Authentication 객체를 받아 인증 처리를 한다.
4. 인증이 실패 한 경우 예외가 발생하고, 인증에 성공하면 Authentication 객체를 새로 생성한다.
5. SecurityContext 안에 Authentication 생성된 객체를 저장한다. 이렇게 함으로써 전역적으로 참조할 수 있게 된다.