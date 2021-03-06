   통신에 관한 국제 표준기구인 ISO (International Organization for Standardization) 에서 정의한 개념. 통신을 7개의 단계별로 표준화하여 그 효율성을 높였다. 네트워크를 7계층으로 나눌 경우 다음과 같은 이점이 있다.

1. 데이터의 흐름을 한 눈에 파악할 수 있다.
2. 문제를 해결하기가 편리하다. (네트워크에서 문제가 발생하면 7개의 작은 문제로 나누어 해결할 수 있다.)
3. 계층별 표준화 덕에 여러 회사의 장비를 사용해도 네트워크가 이상 없이 돌아갈 수 있다.



### 1 계층 (Physical Layer)

통신의 맨 아래 단계. 전기적, 기계적, 기능적인 특성을 이용하여 통신 케이블로 데이터를 전송한다. 통신 단위는 비트 (1 - On, 0 - Off) 데이터를 전달만 할 뿐 다른 것에는 관여하지 않는다. 케이블, 리피터, 허브 등이 대표적 장비



### 2 계층 (Data Link Layer)

피지컬 레이어를 통해 송, 수신 되는 정보의 오류와 흐름을 관리하여 안전한 정보의 수행을 돕는 역할을 한다. 따라서 오류를 찾고, 재전송 하고, 맥 어드레스로 통신을 할 수 있게 해준다. 이 계층에서 전송되는 단위를 '프레임' 이라 한다. 브리지, 스위치 등이 대표적 장비



### 3 계층 (Network Layer)

데이터를 목적지까지 안전하고 빠르게 전달하는 기능을 수행한다. (이 것을 '라우팅' 이라 한다.) 경로를 선택하고, 주소를 정하고, 경로에 따라 패킷을 전달해주는 것이 네트워크 계층의 역할이다. 라우터가 대표적 장비



### 4 계층 (Transport Layer)

```
플로 컨트롤과 에러 복구 기능을 담당한다. 즉, 에러 복구를 위해 패킷을 재전송 하거나 플로를 조절해서 데이터가 정상적으로 전송될 수 있도록 하는 역할을 한다.
```

TCP, UDP가 이 계층에 포함된다.



### 헤더

데이터가 전송되는 과정에서 각 계층을 내려가며 '헤더' 라는 정보가 붙게 된다.