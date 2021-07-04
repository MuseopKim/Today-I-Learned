사용자로부터 요청을 받으면 Authentication 인증 객체가 존재 하는지 검사를 하게 된다.

만약 존재하지 않는다면 해당 사용자를 익명 사용자로 판단하고, 익명 사용자용 인증객체(AnonymousAuthenticationToken)를 생성한다. 이는 이후 SecurityContext안에 저장 된다(ROLE_ANONYMOUS)

SecurityContext에 AnonymousAuthenticationToken 인증 객체를 저장하긴 하지만, 인증 객체를 세션에 저장하지는 않는다.

해당 필터로 익명 사용자와 인증 사용자를 구분해서 처리할 수 있는데, 한가지 예를 들면 화면에서 인증 여부를 구현해야 할 때 isAnonymous(), isAuthenticated() 메서드를 사용할 수 있다.

