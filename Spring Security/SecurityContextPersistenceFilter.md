## SpringContextPersistenceFilter

SecurityContext를 생성, 저장, 조회 하는 필터



### 익명 사용자

익명 사용자가 요청을 하면 새로운 SecurityContext 객체를 생성하고, SecurityContextHolder에 저장한다. AnonymousAuthneticationFilter에서 AnonymousAuthenticationToken 객체를 SecurityContext 객체에 저장한다.



### 인증 (폼 로그인)

새로운 SecurityContext를 생성하고 SecurityContextHolder에 저장한다. 인증에 성공했을 경우UsernamePasswordAuthenticationFilter가 SecurityContext에 UsernamePasswordAuthenticationToken 객체를 저장한다. 인증이 최종 완료되면 Session에 SecurityContext를 저장한다.



### 인증 이후

Session에서 SecurityContext를 찾고, SecurityContextHolder에 저장한다. SecurityContext 안에 Authentication 객체가 존재하면 인증을 유지한다.



### 최종 응답 시

SecurityContextHolder.clearContext() 로 SecurityContext를 제거한다.

- HttpSecurityContextRepository : SecurityContextPersistenceFilter 내부에 존재하며 SecurityContext를 생성, 저장하는 실제 객체