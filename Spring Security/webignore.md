## webIgnore

클라이언트가 서버에 요청한 자원이 js, html, css와 같은 정적 자원일 경우에도 시큐리티는 보안 검사를 실행한다. 따라서 보안 필터를 적용 할 필요가 없는 리소스들에 대해서 ignore 설정을 별도로 해주어야 한다.

WebSecurity 에서 ignoring을 하므로 보안 필터 자체를 들어가지 않는다.

**WebIgnore**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

		@Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
    }
		// ...
} 
```