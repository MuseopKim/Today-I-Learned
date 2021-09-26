## UserDetailsService

Username으로 사용자를 찾아본 뒤 사용자가 DB에 없을 경우 예외를, 찾았을 경우 새로운 시큐리티 사용자 타입의 객체(UserDetails 를 구현한 User 객체) 를 생성해서 리턴한다.



**CustomUserDetailsService**

```java
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private AccountRepository accountRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Account account = accountRepository.findByUsername(username);

        if (account == null) {
            throw new UsernameNotFoundException("Username not found exception");
        }

        List<GrantedAuthority> roles = new ArrayList<>();
        roles.add(new SimpleGrantedAuthority("ROLE_USER"));

        AccountContext accountContext = new AccountContext(account, roles);

        return accountContext;
    }
}
```



**AccountContext**

```java
public class AccountContext extends User {

    private final Account account;

    public AccountContext(Account account, Collection<? extends GrantedAuthority> authorities) {
        super(account.getUsername(), account.getPassword(), authorities);
        this.account= account;

    }

    public Account getAccount() {
        return account;
    }
}
```



**SecurityConfig**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService);
    }
		// ...
}
```