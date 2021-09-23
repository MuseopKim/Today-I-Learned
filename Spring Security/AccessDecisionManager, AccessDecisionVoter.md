## AccessDecisionManager

인증, 요청, 권한 정보를 활용하여 자원 접근을 허용 할 것인지 거부 할 것인지를 결정한다. 여러개의 Voter를 가질 수 있고, Voter는 허용, 거부, 보류에 해당하는 값들을 리턴한다.



### 접근 결정의 유형

- AffirmativeBased : 하나의 Voter라도 허용하면 최종 승인되는 것으로 판단

- ConsensusBased : 다수결에 의해 최종 승인 판결 (동수의 경우 allowIfEqualGrantedDeniedDecisions의 값이 true인지, false인지에 따라 결정)

- UnanimousBased : 만장일치 되어야 승인 되는 것으로 판단

  

## AccessDecisionVoter

판단을 심사하여 AccessDecisionManager에게 판단 결과를 리턴한다.



### 판단 근거

- Authentication : 인증 (user)

- FilterInvocation : 요청 정보 - antMatcher("/user")

- ConfigAttributes : 권한 정보 - hasRole("USER")

  

### 판단 결과

- ACCESS_GRANTED (1)
- ACCESS_DENIED : 접근 거부 (-1)
- ACCESS_ABSTAIN : 접근 보류 (0)

