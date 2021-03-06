## VLAN의 필요성

네트워크 영역의 구분은 스위치의 능력을 뛰어넘는 기능이었다. 브로드캐스트 도메인을 나누려면 중간에 라우터를 두고 양쪽으로 스위치를 라우터에 연결해야만 한다. 그런데 VLAN을 사용하면 한 대의 스위치를 마치 여러대의 분리된 스위치처럼 사용하고, 여러 개의 네트워크 정보를 하나의 포트를 통해 전송할 수 있다. 즉, 하나의 스위치에 연결된 장비들도 브로드캐스트 도메인을 서로 달리할 수 있다.

⇒ VLAN이 지원되는 라우터와 스위치를 사용하면 라우터는 스위치로 하나의 링크만을 이용해서도 3개의 네트워크 정보를 같이 실어보낼 수 있다. 즉 한 선에 여러 개의 네트워크 정보를 보내는 것이 가능해진다.

---

## VLAN의 특징

- VLAN은 스위치에서 지원하는 기능이다. 허브나 브리지에서는 지원하지 않으므로 VLAN을 지원 한다면 이 장비는 스위치 이상의 장비이다.
- VLAN은 한 대의 스위치를 여러 개의 네트워크로 나누기 위해 사용한다. 따라서 나누어진 VLAN 끼리의 통신은 오직 라우터를 통해서만 가능하다.
- VLAN은 스위치에 존재하는 각각의 스위치이다.
- 트렁크 포트가 가능하기 때문에 VLAN은 여러 대의 스위치에 구성이 가능하다. 즉 같은 VLAN 끼리는 스위치를 건너서도 통신이 가능한 것이다.

### 트렁크 포트

하나의 포트를 통해 여러 개의 VLAN을 전송할 수 있게 하는 포트

---

## VTP (VLAN Trunking Protocol)

### 트렁킹

여러 개의 VLAN들을 함께 실어 나르는 것. 모든 VLAN이 하나의 링크를 통해 다른 스위치나 라우터로 이동하기 위한 것. 표준 프로토콜로 IEEE 802.1Q 방식을 사용한다.

VLAN은 하나의 링크에 다양한 패킷을 실어나르기 때문에 목적지 구분을 위해 전송 시점에 각각의 패킷에 자신들의 VLAN 정보를 붙이게 된다.

### 네이티브 VLAN

패킷에 VLAN 정보를 붙이지 않고 보내는 VLAN. 스위치 네트워크에서 유일하게 한 개의 VLAN만을 네이티브 VLAN으로 세팅할 수 있다.

### VTP

스위치들 간에 VLAN 정보를 서로 주고받아 스위치들이 가지고 있는 VLAN 정보를 항상 일치시켜 주기 위한 프로토콜. VLAN 정보가 변할 경우 각각의 스위치 구성을 별도로 해주어야 하는데, VTP를 이용하면 서버에서 한번만 VLAN 정보를 설정해도 다른 스위치와의 트렁크 링크를 통해서 VLAN 정보를 자동으로 업데이트 한다.

### VTP의 세가지 모드

- VTP 서버 모드 : VLAN을 생성하고, 삭제하고, VLAN의 이름을 바꿔 줄 수 있으며, VTP 도메인 안에 있는 나머지 스위치들에게 VTP 도메인 이름과 VLAN 구성, Configuration Revision 넘버를 전달해 줄 수 있다. VTP 서버는 VTP 도메인의 모든 VLAN에 대한 정보를 NVRAM에서 관리하고, 스위치가 꺼졌다 다시 켜지더라도 VLAN 정보를 모두 가직 ㅗ있다.
- VTP 클라이언트 모드 : VLAN을 만들거나 삭제하고, VLAN 이름을 바꿔주는 일이 불가능하다. VTP 서버가 전달해준 VLAN 정보를 받고, 받은 정보를 자기와 연결된 다른 쪽 스위치에 전달하는 것만 가능하다. 정보를 NVRAM에 저장하지 않기 때문에 스위치가 리부팅하면 모든 VLAN 정보를 잃게 된다.
- VTP 트랜스페어런트 모드 : VTP 도메인 영역 안에 있지만, 서버로부터 메시지를 받아 자신의 VLAN을 업데이트 하거나 자신의 VLAN 업데이트 정보를 다른 스위치에 전달하지 않는다. 따라서 직접 VLAN을 만들고 삭제할 수 있으며, 이 정보를 자기만 알면 되기 때문에 다른 스위치들에게 알리지 않는다. 서버를 통해서 들어온 메시지를 자기를 통해 연결된 다른 스위치 쪽으로 전달 해주거나 자기와 연결된 다른 스위치쪽에서 서버쪽으로 가는 VTP 메시지를 전달해주는 역할만 한다. 로컬 스위치에서만 사용할 VLAN을 가진 스위치에 주로 사용한다.

---

## VLAN의 구성

1. 도메인 이름을 설정한다.
2. VTP 모드를 입력한다. (네트워크에 VTP 서버 모드로 동작하는 스위치가 있다면 클라이언트로 설정이 가능하지만, 없다면 서버 혹은 트랜스페어런트로만 설정할 수 있다.)
3. 트렁크 포트를 세팅한다. (IEEE 802.1Q 또는 ISL, 트렁크는 나와 상대가 모두 서로 같은 모드를 써야 통신 된다.)
4. VLAN을 만든다.
   - 아무런 설정을 하지 않으면 모든 포트가 다 디폴트 VLAN 1에 설정되어 있다.
   - 각 스위치 종류마다 만들 수 있는 VLAN의 개수는 다르다.
   - 스위치의 IP 주소 세팅은 VLAN 1에 한다. 스위치는 라우터처럼 인터페이스마다 IP 주소를 주지 않는다.
   - VLAN을 추가 또는 삭제하는 작업은 VTP 서버 모드 또는 VTP 트랜슾어런트 모드에서만 가능하다.
5. VLAN에 포트를 배정한다.
