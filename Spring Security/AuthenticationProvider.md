## AuthenticationProvider

CustomUserDetailsService가 반환한 UserDetails를 검증하여 인증 처리를 하는 AuthenticationProvider를 커스터마이징 한다.

**CustomAuthenticationProvider**

```java
public class CustomAuthenticationProvider implements AuthenticationProvider {

    @Autowired
    private UserDetailsService userDetailsService;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {

        String username = authentication.getName();
        String password = (String) authentication.getCredentials();

        AccountContext accountContext = (AccountContext) userDetailsService.loadUserByUsername(username);

        if (passwordEncoder.matches(passwordEncoder.encode(password), accountContext.getAccount().getPassword())) {
            throw new BadCredentialsException("Bad credential exception");
        }

        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(accountContext.getAccount(), null, accountContext.getAuthorities());

        return authenticationToken;
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
    }
}
```

- authenticate() : 실제 인증 과정을 정의

- supports() : Authenticaiton 타입과 CustomAuthenticationProvider가 사용하려는 Authentication 타입이 일치할 때만 인증 처리를 하도록 조건을 설정

  

**SecurityConfig**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

		@Override
		protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		    auth.authenticationProvider(authenticationProvider());
		}
		
		@Bean
		public AuthenticationProvider authenticationProvider() {
		    return new CustomAuthenticationProvider();
		}
}
```

- 이미 CustomAuthenticationProvider 에서 CustomUserDetailsService를 참조하고 사용하고 있으므로 관련 설정을 삭제 했다.

  

### 동작 과정

1. 요청
2. AuthenticationFilter
3. UserDetailsService - DB로부터 사용자 조회)
4. AuthenticationProvider - 인증, Authentication 반환)
5. AuthenticationFilter - SecurityContext 생성 및 Authentication 저장)
6. SecurityContextHolder에 해당 SecuirtyContext 저장