## ExceptionTranslationFilter

FilterSecurityInterceptor로부터 예외를 전달 받는다. 예외처리는 크게 두 가지의 유형으로 나눌 수 있는데, 인증 예외 처리를 담당하는 AuthenticationException, 인가 예외 처리를 담당하는 AccessDeniedException가 있다.

### AuthenticationException

인증 예외 처리 담당

1. AuthenticationEntryPoint 구현체 호출 : 로그인 페이지 이동, 오류 코드 전달

2. 인증 예외가 발생하기 전의 요청 정보를 저장

   - RequestCache : 사용자의 이전 요청 정보를 세션에 저장하고 꺼내 오는 캐시 메커니즘

     - SavedRequest : 사용자가 요청했던 Request 파라미터 값, 헤더 값 저장

     

### AccessDeniedException

인가 예외 처리

- AccessDeniedHandler에서 예외를 처리

```java
http.exceptionHandling()
		.authenticationEntryPoint(authenticationEntryPoint())   // 인증
		.accessDeniedHandler(accessDeniedHandler())     // 인가
```



------

## RequestCacheAwareFilter

SavedRequest 객체가 존재하는지 확인하는 필터. 객체가 존재 할 경우 해당 객체를 계속해서 참조할 수 있도록 처리한다.