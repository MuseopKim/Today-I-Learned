## AuthenticationManager

인증 처리의 위임을 당당하는 인터페이스. ProviderManager로 구현.



AuthenticationProvider 목록에서 알맞는 Provider(Form인증, RememberMe 인증, OAuth 인증 담당 등)를 선택하여 인증을 위임한다.



선택된 ProviderManager가 해당 인증 방식을 AuthenticationProvider에 위임할 수 없는 경우 부모 ProviderManager에게 위임 권한이 넘어가며, 이후 계속 AuthenticationProvider를 탐색한다.



AuthenticationProvider에서 인증이 성공하면 ProviderManager에게 Authentication을 전달하고, AuthenticationProvider는 해당 객체를 Filter로 다시 전달한다.