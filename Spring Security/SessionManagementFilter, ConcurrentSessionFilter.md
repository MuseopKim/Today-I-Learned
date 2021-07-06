## SessionManagementFilter의 기능

1. 세션 관리 : 인증 시 사용자의 세션정보를 등록, 조회, 삭제 등의 세션 이력을 관리
2. 동시적 세션 제어 : 동일 계정으로 접속이 허용되는 최대 세션수를 제한
3. 세션 고정 보호 : 인증 할 때마다 세션 쿠키를 새로 발급하여 공격자의 쿠키 조작을 방지
4. 세션 생성 정책 : Always, If-Required, Never, Sateless



## ConcurrentSessionFilter

SessionManagementFilter와 함께 연계해서 동시적 세션 제어

- 매 요청마다 현재 사용자의 세션 만료 여부 체크
- 세션 만료시 즉시 만료 처리
- session.isExpired() == true ⇒ 로그아웃 처리, 즉시 오류 페이지 응답 (This session has been expired)



## 동작 방식

1. 로그인을 시도 할 경우 SessionManagementFilter로 접근
2. 사용자가 인증을 완료한 상태에서 다른 사용자가 동일한 계정에 대해 로그인 인증을 시도 할 경우 session.expreNow()에 의해 이전 사용자의 세션이 만료 된다.
3. ConcurrentSessionFilter에서 서버에 접근할 때마다 세션을 확인 한다. 이전 사용자가 다시 서버에 접근할 때 session.isExpired()가 작동하게 되는데, 세션이 만료 되었으므로 로그아웃 처리를 한 뒤 오류 페이지를 응답하게 된다.

- ConcurrentSessionControlAuthenticationStrategy : ConcurrentSessionFilter가 호출하는 동시적 세션 제어를 처리 하는 클래스
- ChangeSessionIdAuthenticationStrategy : 세션 고정 보호를 담당하는 클래스
- RegisterSessionAuthenticationStrategy : 세션을 등록 및 저장

최대 세션 허용 개수를 초과하는 경우 다음의 두 가지 전략을 취할 수 있다.

- 인증 실패 전략 : SessionAuthenticationException 발생
- 세션 만료 전략 : session.expireNow() (사용자1의 세션을 만료 시키는 방법)