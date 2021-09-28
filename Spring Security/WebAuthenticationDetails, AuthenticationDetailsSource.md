## WebAuthenticationDetails

사용자 인증시 username, password와 함께 파라미터를 받아서 저장한 뒤 인증처리 과정에서 추가 작업을 수행할 수 있다. (ex. Secret Key) 이를 담당하는 클래스가 WebAuthenticationDetails이다.

AuthenticationFilter를 거쳐 Authentication 객체가 생성된다. Authentication 객체는 details 속성을 갖는데, 이 곳에 WebAuthenticationDetails 객체가 저장 된다. WebAuthenticationDetails에는 Session ID, 파라미터 데이터가 저장 되어 있다.



**CustomWebAuthenticationDetails**

```java
public class CustomWebAuthenticationDetails extends WebAuthenticationDetails {

    private String secretKey;

    public FormWebAuthenticationDetails(HttpServletRequest request) {
        super(request);
        String secretKey = request.getParameter("secretKey");
    }

    public String getSecretKey() {
        return secretKey;
    }
}
```



## AuthenticationDetailsSource

WebAuthenticationDetails 객체를 생성한다.



**CustomAuthenticationDetailsSource**

```java
@Component
public class FormAuthenticationDetailsSource implements AuthenticationDetailsSource<HttpServletRequest, WebAuthenticationDetails> {

    @Override
    public WebAuthenticationDetails buildDetails(HttpServletRequest context) {
        return new FormWebAuthenticationDetails(context);
    }
}
```



**AuthenticationProvider**

```java
public class FormAuthenticationProvider implements AuthenticationProvider {

		// ...

		@Override
		@Transactional
		public Authentication authenticate(Authentication authentication) throws AuthenticationException {
		
		    String loginId = authentication.getName();
		    String password = (String) authentication.getCredentials();
		
		    AccountContext accountContext = (AccountContext) userDetailsService.loadUserByUsername(loginId);
		
		    if (passwordEncoder.matches(passwordEncoder.encode(password), accountContext.getAccount().getPassword())) {
		        throw new BadCredentialsException("Bad credential exception");
		    }
		
		    FormWebAuthenticationDetails formWebAuthenticationDetails = (FormWebAuthenticationDetails) authentication.getDetails();
		    String secretKey = formWebAuthenticationDetails.getSecretKey();
		    if (secretKey == null || !"secret".equals(secretKey)) {
		        throw new InsufficientAuthenticationException("InsufficientAuthenticationException");
		    }
		// ...

}
```

