## SecurityContext

User 객체를 담고 있는 Authentication 객체가 저장되는 보관소. 필요시 언제든 찾아와 사용할 수 있다. ThreadLocal에 저장되기 때문에 아무 곳에서나 참조가 가능하다.

사용자가 인증이 되면 HttpSession에 저장되어 애플리케이션 전반에서 참조가 가능하다.



**ThreadLocal**

각 쓰레드에 할당 되어 있는 저장소. 쓰레드간에 공유되지 않아 쓰레드 세이프 하다.



## SecurityContextHolder

SecurityContext를 저장하고 있는 보관소. 다음과 같이 다양한 저장 방식이 있다.

- MODE_THREADLOCAL : 스레드당 SecurityContext 객체를 할당한다. 기본 값.
- MODE_INHERITABLETHREADLOCAL : 메인 스레드와 자식 스레드에서 동일한 SecurityContext를 유지한다.
- MODE_GLOBAL : 애플리케이션에서 단 하나의 SecurityContext를 저장한다.



### 사용자 인증 과정

1. 사용자가 로그인 요청을 한다.

2. 서버가 스레드 하나를 생성해서 해당 요청을 받는다. (해당 스레드는 스레드 로컬을 가지고 있다.)

3. Authentication 객체를 생성한다.

4. 인증이 실패하면 SecurityContextHolder.clearContext() 로 SecurityContext를 null로 초기화 한다. 인증이 성공하면SecurityContext에 Authentication 객체를 저장한다.

5. 앞서 설정 된 SecurityContextHolder를 스레드 로컬에 저장한다.

6. SecurityContext가 HttpSession에 'SPRING_SECURITY_CONTEXT' 라는 이름으로 저장된다.

   최종적으로 인증이 완료된 사용자의 Authentication 객체가 담겨 있는 SecurityContext가 HttpSession에 저장된다. 만약 이후 사용자가 해당 JSESSIONID와 함께 재요청을 할 경우 일치하는 세션을 HttpSession에서 찾아온 뒤 해당 SecurityContext를 스레드 로컬에 저장하고 이후 처리를 진행한다.

   

**SecurityContext**

```java
@RestController
public class SecurityController {

	@GetMapping("/")
  public String index(HttpSession session) {
      Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
      SecurityContext context = (SecurityContext) session.getAttribute(HttpSessionSecurityContextRepository.SPRING_SECURITY_CONTEXT_KEY);
      Authentication authentication1 = context.getAuthentication();
      return "home";
  }
}
```

SecurityContextHolder의 스레드 기본 설정은 MODE_THREADLOCAL이다. 이는 메인 스레드와 자식 스레드간에 스레드 로컬을 공유하지 못한다. 만약 변경하고 싶다면 MODE_INHERITABLETHREADLOCAL 로 다음과 같이 변경한다.



**Thread Local**

```java
@Configuration
@EnableWebSecurity
public class ThreadModeSecurityConfig extends WebSecurityConfigurerAdapter {

    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .anyRequest().authenticated();
        http
                .formLogin()
                ;

        SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);
    }
}
```