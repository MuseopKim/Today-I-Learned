## HTTP 커넥션

---



## Connection 헤더

HTTP는 클라이언트와 서버 사이에 프락시 서버, 캐시 서버 등과 같은 중개 서버가 놓이는 것을 허락한다. HTTP 메시지는 클라이언트 → 서버 과정에서 중개 서버들을 하나하나 거치며 전달 된다. Connection 헤더 필드는 다른 커넥션에 전달 되지 않는다. Connetion 헤더에는 다음 세 가지 종류의 토큰이 전달 될 수 있다.

1. HTTP 헤더 필드 명 : 이 커넥션에만 해당 되는 헤더들을 나열 한다.
2. 임시적 토큰 값 : 커넥션에 대한 비표준 옵션
3. close 값 : 작업이 종료 되면 커넥션이 종료 되어야 함을 의미



---



## HTTP 커넥션의 성능 향상 기술

### 벙렬 커넥션

   클라이언트가 여러 개의 커넥션을 맺음으로써 여러 개의 HTTP 트랜잭션을 병렬로 처리 할 수 있다. 단일 커넥션의 대역폭 제한과 커넥션이 동작하지 않고 있는 시간을 활용하여, 객체가 여러 개 있는 웹페이지를 더 빠르게 로드할 수 있다. 그러나 클라이언트의 네트워크 대역폭이 좁을 때, 제한된 대역폭 내에서 각 객체를 전송 받는 것이 오히려 느릴 수도 있다. 또한 다수의 커넥션은 메모리를 많이 소비하고 성능 문제를 발생 시키므로 제한적 병렬 커넥션만을 허용한다.



### 지속 커넥션

   처리가 완료 된 후에도 계속 연결된 상태로 있는 TCP 커넥션. 해당 서버에 이미 맺어져 있는 지속 커넥션을 재사용함으로써 커넥션을 맺기 위한 준비작업에 따르는 시간을 줄일 수 있다. 또한 TCP 느린 시작으로 인한 성능 저하를 피하게 되므로 더 빠른 성능을 기대할 수 있다. 하지만 지속 커넥션을 잘 못 관리할 경우, 클라이언트와 서버의 리소스에 불필요한 소모를 발생 시킨다. 지속 커넥션은 병렬 커넥션과 함께 사용될 때 가장 효과적이다. 적은 수의 병렬 커넥션만을 맺고 그 것을 유지하는 방법 말이다.



### 파이프라인 커넥션

파이프 라인 커넥션을 통해 keep-alive 커넥션의 성능을 더욱 높여 줄 수 있다. 이는 응답이 도착하기 전까지 큐 스택에 여러 개의 요청을 쌓아놓고, 다수의 요청을 순차적으로 보낼 수 있음을 의미한다.