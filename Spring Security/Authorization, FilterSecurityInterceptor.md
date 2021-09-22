

## Authorization

인증이 완료 된 사용자가 특정 자원에 접근할 때의 권한 여부를 확인 하는 것



### 스프링 시큐리티가 지원하는 권한 계층

### 웹 계층

- URL 요청에 따른 화면 단위의 보안



### 서비스 계층

- 메소드와 같은 기능 단위의 보안



### 도메인 계층

- 객체 단위의 보안



## FilterSecurityInterceptor

   각 필터를 거쳐 권한 심사가 끝난 뒤 자원에 대한 최종 인가 처리를 담당하는 필터. 승인 / 거부 여부를 최종적으로 결정한다. 만약 인증 객체 없이 자원에 접근할 경우 AuthenticationException을 발생 시키고, 권한이 없을 경우 AccessDeniedException 예외를 발생 시킨다.

1. 요청이 들어오고 FilterSecurityInterceptor에게 전달 되면, 인증 객체 여부를 검증한다.
2. 만약 인증 객체가 없다면 AuthenticationException을 발생 시키고 인가 과정을 중지한다. 만약 인증 객체가 존재하면 SecurityMedtadataSource에 전달된다.
3. 자원에 대한 권한 정보를 확인한다. 권한이 필요하지 않는 자원에 대해서는 권한 심사를 멈추고 자원에 대한 접근을 허용 시킨다. 만약 권한이 필요한 자원이면, AccessDecisionManager에게 전달하여 최종 권한 심사를 한다.

