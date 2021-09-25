## PasswordEncoder

평문인 비밀번호를 암호화 하여 저장할 수 있도록 해준다. Spring Security 5.0 이전에는 평문을 지원하는 NoOpPasswordEncoder가 있었으나, 현재는 Deprecated 되었다.

여러개의 PasswordEncoder 유형 중 하나를 선택하여 생성한다.



**PasswordEncoder 생성**

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return PasswordEncoderFactories.createDelegatingPasswordEncoder();
}
```



### 암호화 포맷

`{id}encodedPassword` 형식이며, 기본 포맷은 Bcrypt이다.



### 인터페이스 메서드

- encode(password) : 패스워드를 암호화한다.

- matches(rawPassword, encodedPassword) : 패스워드를 비교한다.

  

**Encode password**

```java
@PostMapping(value="/users")
public String createUser(AccountDto accountDto) throws Exception {

	ModelMapper modelMapper = new ModelMapper();
	Account account = modelMapper.map(accountDto, Account.class);
	account.setPassword(passwordEncoder.encode(accountDto.getPassword()));

	userService.createUser(account);

	return "redirect:/";
}
```