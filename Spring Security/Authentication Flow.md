## Authentication Flow

### [1] UsernamePasswordAuthenticationFilter

폼 로그인 인증 방식으로 인증을 시도할 때 사용자의 요청이 들어오면 동작한다. 사용자 로그인 요청을 바탕으로 Authentication (사용자 입력 username + password) 인증 객체를 생성한다. 이후 AuthenticationManager로 해당 Authentication 객체를 전달한다.



### [2] AuthenticationManager

Authentication 객체를 전달받아 인증의 전반을 관리한다. 실제 인증(검증)을 수행하는 것이 아니라 `List<AuthenticationProvider>` 중 적절한 하나의 Provider에 에 인증을 위임한다.



### [3] AuthenticationProvider

Authentication 객체(username + password) 를 검증한다. 먼저, `loadUserByUsername(username)` 으로 UserDetailsService에 유저 객체를 요청한다.



### [4] UserDetailsService

UserDetailsService가 Repository에서 유저 객체를 검색하고, username에 해당하는 유저가 Repository에 존재하면 UserDetailsService로부터 UserDetails 타입 객체가 생성되어 AuthenticationProvider에 전달 된다. 만약 유저가 존재하지 않을 경우 예외가 발생한다. 해당 예외는 UsernamePasswordAuthenticationFilter에 전달되어 처리 된다.



### [5] AuthenticationProvider

전달 받은 UserDetails 객체에 담겨 있는 password 정보와 사용자가 요청한 password 정보가 일치하는지 검증한 뒤, 일치 할 경우 UserDetails, authorities 데이터를 담는 Authentication을 새로 생성 후 AuthenticationManager에게 전달한다.



### [6] AuthenticationManager

전달 받은 Authentication 객체를 UsernamePasswordAuthenticationFilter에 전달한다.



### [7] UsernamePasswordAuthenticationFilter

최종 Authentication 객체를 SecurityContext에 저장한다.