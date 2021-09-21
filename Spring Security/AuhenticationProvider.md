## AuthenticationProvider

ID, Password 검증 등 실질적인 인증 처리를 담당하는 인터페이스



### ID 검증

- UserDetailsService : Authentication 객체에 담겨 있는 username으로 저장소에 일치하는 유저 정보가 있는지 확인한다. 있을 경우 UserDetails 객체를 만들어 AuthenticationProvider에게 반환하고, 없을 경우 UserNotFoundException이 Filter로 전달된다.

  

### Password 검증

- 사용자의 요청 Password와 UserDetails 객체에 담겨 있는 Password를 비교한다. 일치하지 않으면 BadCredentialException이 발생하고, Filter로 전달된다.

  

### 추가 검증

- Password까지 검증이 완료 되면 Authentication(user, authorities) 객체가 생성되며, 해당 객체가 AuthenticationManager로 전달 된다.