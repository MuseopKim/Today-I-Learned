## 서블릿 컨테이너와 스프링 컨테이너

클라이언트에 요청이 들어오면 Servlet Filter를 거쳐 Servlet에서 해당 요청을 처리하게 되고, 마찬가지로 Servlet Filter를 거쳐 클라이언트에 응답이 전달 된다. Filter는 Servlet 스펙에 정의되어 있는 기술이기 때문에 Servlet 스펙을 지원하는 컨테이너에서 생성이 되고 실행 된다.

스프링과 Servlet은 서로 다른 컨테이너이기 때문에 Servlet Filter는 스프링의 빈을 주입 받거나 스프링의 기술을 사용할 수 없다. 스프링에서 정의된 빈을 주입해서 사용할 수 없기 때문에 특정한 이름의 빈을 찾아 해당 요청을 위임한다.



## DelegatingFilterProxy

DelegatingFilterProxy는 Servlet Filter로부터 요청을 받은 뒤 특정한 이름을 가진 스프링 빈에게 요청을 위임 해준다.

스프링 시큐리티는 Filter를 기반으로 인증, 인가 처리를 하게 되는데, 이 때문에 해당 되는 스프링 빈을 먼저 만들고 Servlet Filter를 구현하게 된다. 하지만 스프링 빈이 Servlet Filter를 구현했다 하더라도 클라이언트의 요청을 직접 받을 수는 없고 Servlet Filter를 통해 스프링 빈에 요청이 전달 되는데, 이 때 요청을 스프링 빈에게 대신 위임 해주는 것이 DelegatingFilterProxy이다.

정리하자면 스프링 시큐리티는 Servlet Filter와 Spring의 기능을 일부 가지고 있는 복합체라 할 수 있다. 따라서 클라이언트로부터의 요청이 Servlet Filter로 먼저 전달 되고, DelegatingFilterProxy가 그 요청을 받아서SpringSecurityFilterChain 빈에게 위임한다.



## FilterChainProxy

springSecurityFilterChain이라는 이름으로 생성되는 필터 빈이다. DelegatingFilterProxy로부터 요청을 위임 받고, 스프링 시큐리티 초기화 시에 생성(기본, 설정 클래스를 통한 추가)되는 모든 필터들을 관리하고 제어한다. 즉, 실제 보안 처리를 한다 할 수 있다.

사용자의 요청을 Chain으로 연결 된 필터 순서대로 호출하고 전달한다. 필터를 통과하며 마지막까지 예외가 발생하지 않으면 인증 및 인가가 완료 된 것이라 할 수 있다.