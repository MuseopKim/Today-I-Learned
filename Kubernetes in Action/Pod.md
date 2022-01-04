# Pod

파드는 함께 배치된 컨테이너 그룹이며 쿠버네티스의 기본 빌딩 블록이다. 쿠버네티스는 컨테이너를 개별적으로 배포하지 않고 컨테이너의 그룹인 파드를 배포하고 운영한다.

이때 파드가 항상 두 개 이상의 컨테이너를 포함하는 것은 아니며, 하나의 컨테이너만 포함하기도 한다. 파드는 단일 워커 노드에 온전히 구성되며, 여러 워커 노드에 걸쳐서 구성되거나 실행되지 않는다. (- 하나의 파드가 워커노드 1, 워커 노드2에 나누어 걸쳐져 있을 수 없다는 의미)

---

## 파드의 존재 이유

### 여러 프로세스를 실행하는 단일 컨테이너보다 다중 컨테이너가 더 좋다

프로세스는 컨테이너 상에서 실행되고, 각 컨테이너는 격리된 머신과 비슷하기 때문에 여러 프로세스를 단일 컨테이너 안에서 실행하는 것이 자연스럽다고 여길 수 있지만, 실제로는 그렇지 않다.

컨테이너는 단일 프로세스를 실행하는 것을 목적으로 설계 되었다. (프로세스가 자신의 자식 프로세스를 파생하는 것 제외) 단일 컨테이너에서 관련 없는 다른 프로세스를 실행하는 경우 모든 프로세스를 실행하고 로그를 관리하는 것은 모두 사용자의 책임이다. 또한 개별 프로세스가 실패하는 경우 자동으로 재시작하는 메커니즘을 포함해야 한다. 이러한 메커니즘에는 동일한 표준 출력으로 로그를 기록하는데, 어떤 프로세스가 남긴 로그인지 파악하는 것은 매우 어렵다. 따라서 프로세스 자체를 개별 컨테이너로 실행해야하고, 이 것이 컨테이너와 쿠버네티스를 사용하는 올바른 방법이다.

---

## 파드의 기본 이해

앞 서 살펴본 이유로 여러 프로세스를 단일 컨테이너로 묶지 않기 때문에, 이러한 다중 컨테이너를 하나의 단위로 관리할 수 있는 하나의 상위 구조가 요구된다. 이를 위해 사용하는 논리적인 단위가 파드이다.

컨테이너 그룹(파드)을 사용함으로써 밀접하게 연관된 프로세스를 함께 실행하고, 단일 컨테이너에서 모두 함께 실행되는 것과 같은 환경을 제공하면서도, 이들을 격리된 상태로 유지할 수 있다.

### 파드 내부의 컨테이너들은 서로 부분 격리된다

파드는 안에 있는 컨테이너끼리 특정한 리소스를 공유하기 위해 각 컨테이너가 완벽하게 격리되지 않도록 한다. 즉, 쿠버네티스는 파드 안에 있는 모든 컨테이너가 자체 네임스페이스가 아닌 동일한 리눅스 네임스페이스를 공유하도록 컨테이너를 설정한다.

파드의 모든 컨테이너는 동일한 네트워크 네임스페이스, UTS(UNIX Timesharing System Namespace) 네임 스페이스 안에서 실행되기 때문에 모든 컨테이너는 같은 호스트 네임, 네트워크 인터페이스를 공유한다. 또한 모든 컨테이너는 동일한 IPC (Inter-Process Communication) 네임스페이스 아래 실행되어 IPC를 통해 서로 통신을 할 수 있다.

그러나 한가지 다른 것이 있는데, 대부분의 컨테이너 파일시스템은 컨테이너 이미지에 종속되기 때문에 파일시스템 만큼은 다른 컨테이너와 완전히 분리된다. 이를 해결하는 것이 쿠버네티스의 볼륨 개념이다.

### 파드 내부의 컨테이너들은 동일한 IP, 포트 공간을 공유한다

파드 안의 컨테이너는 동일한 네트워크 네임스페이스에서 실행되기 때문에 동일한 IP, 포트공간을 공유한다. 따라서 파드 안의 컨테이너들은 서로 포트 번호가 겹치지 않도록 구성되어야 한다. 하나의 파드 안에 있는 모든 컨테이너들은 동일한 루프백 네트워크 인터페이스를 갖기 때문에 컨테이너들이 로컬호스트를 통해 서로 통신할 수 있다.

### 파드 간 플랫 네트워크

쿠버네티스 클러스터의 모든 파드는 하나의 플랫한 공유 네트워크 주소 공간에 상주한다. 따라서 모든 파드는 다른 파드의 IP 주소를 사용해 접근하는 것이 가능하다.

> 단일 스위치에 연결되어 중간 단계 없이 직접 통신하는 방식과 비슷

LAN 상에 있는 컴퓨터들와 같이 각 파드는 고유 IP를 가지며, 모든 다른 파드에서 이 네트워크를 통해 서로 쉽게 접속할 수 있다.

---

## 파드에서 컨테이너의 적절한 구성

파드는 특정한 애플리케이션만을 호스팅한다. 한 호스트에 모든 유형의 애플리케이션을 넣는게 아닌, 애플리케이션을 여러 파드로 구성하고, 각 파드에는 밀접한 관련이 있는 구성요소를 컨테이너를 통해 구성한다.

### 다계층 애플리케이션을 여러 파드로 분할한다

프론트엔드, 백엔드 두 영역을 단일 파드를 구성 할수도 있겠지만, 노드를 분할 하여 인프라스트럭쳐의 활용도를 높이기 위해서는 적절한 방법이 아니다. 논리적 단위별로 여러 파드로 분할하여 배포하는 것이 더 권장되는 방법이다.

### 개별 확장이 가능하도록 여러 파드로 분할한다

쿠버네티스는 개별 컨테이너를 수평확장 할 수 없으며 파드가 스케일링의 기본 단위가 된다. 컨테이너를 개별적으로 스케일링 하는 것이 필요하다면 반드시 별도의 파드에 배포해야 한다.

### 파드에서 여러 컨테이너를 사용하는 경우

여러 컨테이너를 단일 파드에 넣는 주된 케이스는 주요 프로세스 하나와 하나 이상의 보완 프로세스로 구성되는 경우다.

예를 들어 파드 안에 주 컨테이너는 특정 디렉토리에서 파일을 제공하는 웹 서버이고, 사이드카 컨테이너는 외부 소스에서 주기적으로 콘텐츠를 받아 웹 서버의 디렉토리에 저장하는 API가 되는 것과 같은 구성이 가능하다. 이 때 이 둘은 하나의 볼륨을 공유한다.

### 다중 컨테이너 구성 vs 단일 컨테이너 구성

컨테이너들로 파드 그룹을 구성할 때, 정말 둘 이상의 컨테이너를 단일 파드로 그룹화 하는 것이 적절한지에 대해서 다음과 같은 자가 점검이 필요하다.

- 컨테이너를 함께 실행해야 하는지? 혹은 서로 다른 호스트에서 실행할 수 있는지?
- 여러 컨테이너가 모여 하나의 구성요소인가? 개별적인 구성 요소인가?
- 컨테이너가 함께 스케일링 되는 것이 적절한가?

---

## YAML, JSON 디스크립터 정의 및 파드 생성

파드와 같은 쿠버네티스 리소스는 일반적으로 쿠버네티스 REST API 엔드포인트에 JSON, YAML manifest를 전송하여 생성한다. 또한 YAML 파일을 사용하여 오브젝트를 정의하면 버전 관리가 가능하여 많은 이점이 생긴다.

```bash
# YAML 디스크립터
> kubectl get pod first-kind-multi-node-5bf6ffd7c7-2vvjc -o yaml
```

```yaml
apiVersion: v1
kind: Pod
# ===== Metadata =====
metadata:
  creationTimestamp: "2021-12-30T17:17:53Z"
  generateName: first-kind-multi-node-5bf6ffd7c7-
  labels:
    app: first-kind-multi-node
    pod-template-hash: 5bf6ffd7c7
  name: first-kind-multi-node-5bf6ffd7c7-2vvjc
  namespace: default
  ownerReferences:
    - apiVersion: apps/v1
      blockOwnerDeletion: true
      controller: true
      kind: ReplicaSet
      name: first-kind-multi-node-5bf6ffd7c7
      uid: 15640c66-2ce8-4bb5-823c-894afb10af15
  resourceVersion: "16962"
  uid: 5cf64b27-f56d-4042-b11e-b06323921ae9
# ===== Metadata =====

# ===== Spec =====
spec:
  containers:
    - image: museopkim/kubia
      imagePullPolicy: Always
      name: kubia
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
        - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
          name: kube-api-access-vkxvh
          readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: first-kind-multi-node-cluster-worker
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
    - effect: NoExecute
      key: node.kubernetes.io/not-ready
      operator: Exists
      tolerationSeconds: 300
    - effect: NoExecute
      key: node.kubernetes.io/unreachable
      operator: Exists
      tolerationSeconds: 300
  volumes:
    - name: kube-api-access-vkxvh
      projected:
        defaultMode: 420
        sources:
          - serviceAccountToken:
              expirationSeconds: 3607
              path: token
          - configMap:
              items:
                - key: ca.crt
                  path: ca.crt
              name: kube-root-ca.crt
          - downwardAPI:
              items:
                - fieldRef:
                    apiVersion: v1
                    fieldPath: metadata.namespace
                  path: namespace
# ===== Spec =====

# ===== Status =====
status:
  conditions:
    - lastProbeTime: null
      lastTransitionTime: "2021-12-30T17:17:53Z"
      status: "True"
      type: Initialized
    - lastProbeTime: null
      lastTransitionTime: "2021-12-30T17:18:48Z"
      status: "True"
      type: Ready
    - lastProbeTime: null
      lastTransitionTime: "2021-12-30T17:18:48Z"
      status: "True"
      type: ContainersReady
    - lastProbeTime: null
      lastTransitionTime: "2021-12-30T17:17:53Z"
      status: "True"
      type: PodScheduled
  containerStatuses:
    - containerID: containerd://834f6f5b286849d87d598879e4c47e3318dd24aa4111b496d9edd15d9062e26d
      image: docker.io/museopkim/kubia:latest
      imageID: docker.io/museopkim/kubia@sha256:b98db8cfa5b3c834d7c0b7f7cc2a92f96eeb2b8173964cfdfd375916723afc31
      lastState: {}
      name: kubia
      ready: true
      restartCount: 0
      started: true
      state:
        running:
          startedAt: "2021-12-30T17:18:47Z"
  hostIP: 172.18.0.3
  phase: Running
  podIP: 10.244.1.4
  podIPs:
    - ip: 10.244.1.4
  qosClass: BestEffort
  startTime: "2021-12-30T17:17:53Z"
# ===== Status =====
```

- `Metadata` : 이름, 네임스페이스, 레이블 및 파드에 관한 정보
- `Spec` : 파드 컨테이너, 볼륨 등 파드 자체에 관한 실제 명세
- `Status` : 파드의 상태, 각 컨테이너 설명 및 상태, 파드 내부 IP 등 현재 실행 중인 파드에 관한 정보

---

## 파드를 정의하는 YAML 작성

```yaml
apiVersion : v1   # 디스크립터가 쿠버네티스 API 버전 v1을 준수
kind: Pod   # 오브젝트의 종류가 Pod임을 명시
metatdata:
	name: museop-kubia-manual   # Pod의 이름
spec:
	containers:
	- image: museopkim/kubia   # 컨테이너를 만드는 이미지
		name: kubia   # 컨테이너 이름
		ports:
		- containerPort: 8080   #애플리케이션이 수신하는 포트 번호
			protocol: TCP
```

### 컨테이너의 포트 지정

컨테이너가 0.0.0.0에 열어 둔 포트를 통해 접속을 허용할 경우 파드 스펙에 포트를 명시하지 않아도 다른 파드에서 항상 해당 파드에 접속할 수 있다. 하지만 포트를 명시적으로 정의하면 클러스터를 사용하는 모든 사용자가 파드에서 노출한 포트를 빠르게 볼 수 있다. (// 현재는 이해가 잘 안되는 부분)

### 오브젝트의 스펙 확인하기

```bash
> kubectl explain pod.spec
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26140b1b-2900-4e62-8f83-89ebeb084f76/Untitled.png)

---

## 파드 생성하기

```bash
> kubectl create -f museop-kubia-manual.yaml
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b2c2036-0b33-4a75-a0f4-3b15d2502aa3/Untitled.png)

---

## 애플리케이션 로그 보기

도커는 표준 출력, 표준 에러 스트림을 파일로 전달하고, 다음 명령을 이용해 컨테이너 로그를 가져온다.

```bash
> docker logs [CONTAINER_ID]
```

반면 쿠버네티스는 파드 로그를 기록하고 확인할 수 있다. 만약 단일 컨테이너로 구성된 파드일 경우 로컬 머신에서 다음 명령어를 실행함으로써 간단히 얻을 수 있다.

```bash
> kubectl logs museop-kubia-manual
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/79a60120-8ea9-4682-81eb-68c700088728/Untitled.png)

### 컨테이너 이름을 지정해 다중 컨테이너 파드에서 로그 가져오기

여러 컨테이너를 포함하는 파드인 경우 컨테이너 이름을 옵션에 지정하면 특정 컨테이너의 로그를 가져올 수 있다.

```bash
> kubectl logs museop-kubia-manual -c kubia
```

이러한 방법들은 현재 존재하는 파드의 컨테이너 로그만 가져올 수 있는데, 파드가 삭제되면 해당 로그들도 함께 삭제된다. 따라서 삭제된 후에도 로그를 보기 위해서는 모든 로그를 중앙 저장소에 저장하는(클러스터 전체의 중앙집중식) 로깅을 설정해야 한다.

---

## 파드에 요청 보내기

파드에 테스트와 디버깅 목적으로 연결할 수 있는 방법중 하나가 포트 포워딩이다.

### 로컬 네트워크 포트를 파드의 포트로 포워딩하기

서비스를 거치지 않고 특정 파드에 요청을 하고 싶을 때, 쿠버네티스는 해당 파드로 향하는 포트 포워딩을 구성해준다. 다음 명령은 로컬 포트 8888을 파드의 8080 포트로 향하게 한다.

```bash
> kubectl port-forward museop-kubia-manual 8888:8080
```

다른 터미널에서 curl을 이용하여 kubectl port-forward 프록시를 통해 HTTP 요청을 보내보자.

```bash
> curl localhost:8888
```

---

## 레이블을 이용한 파드 구성

파드의 수가 증가함에 따라 파드를 분류할 필요성이 생긴다. 모든 개발자, 시스템 관리자는 어떤 파드가 어떤 것인지 쉽게 알 수 있도록 임의의 기준 하에 작은 그룹으로 만들 수 있는 방법이 필요하다. 레이블을 사용하면 파드와 다른 쿠버네티스 오브젝트를 논리적으로 그룹화 할 수 있다.

### 레이블

레이블은 파드와 모든 다른 쿠버네티스 리소스를 조직화 할 수 있다. 레이블은 리소스에 첨부하는 키 값 쌍이며, 이 쌍으로 레이블 셀렉터를 사용해 리소스를 선택한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd85f315-95e0-4a40-a281-07bed12be92d/Untitled.png)

### 파드 생성 시 레이블 지정

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: museop-kubia-manual-v2
  labels:
    creation_method: manual # 레이블 지정
    env: prod
spec:
  containers:
    - image: museopkim/kubia
      name: kubia
      ports:
        - containerPort: 8080
          protocol: TCP
```

```bash
# 파드 실행
> kubectl create -f museop-kubia-manual-with-labels.yml
```

```bash
# 레이블 표시
> kubectl get pods --show-labels
```

```bash
# 파드별 특정 레이블 조회
> kubectl get pods -L creation_method,env
```

### 기존 파드 레이블 수정

```bash
# 레이블 추가
> kubectl label pods museop-kubia-manual creation_method=manual

# 기존 레이블 수정
> kubectl label pods museop-kubia-manual-v2 env=debug --overwrite
```

---

## 레이블 셀렉터의 활용

레이블 셀렉터는 특정 레이블로 태그된 파드의 부분 집합을 선택해 원하는 작업을 수행한다. 레이블 셀렉터는 특정 값과 레이블을 갖는지 여부에 따라 리소스를 필터링 한다. 필터링 조건은 다음과 같다.

- 특정한 키를 포함하거나 포함하지 않는 레이블
- 특정한 키와 값을 가진 레이블
- 특정한 키를 갖고 있지만 다른 값을 가진 레이블

### 레이블 셀렉터를 활용한 파드 나열

앞서 지정한 `creation_method` 를 기준으로 파드를 보려면 다음의 명령을 실행한다.

```bash
> kubectl get pods -l creation_method=manual
```

`env` 레이블을 지니고 있지만 값은 고려하지 않는 파드 셀렉트

```bash
> kubectl get pods -l env
```

`env` 레이블을 지니고 있지 않는 파드 셀렉트

```bash
> kubectl get pods -l '!env'
```

`creation_method` 레이블을 지닌 파드 중 값이 manual이 아닌 것

```bash
> kubectl get pods -l creation_method!=manual
```

### 레이블 셀렉터에서 여러 조건 사용

셀렉터는 쉼표로 구분 된 여러 기준을 포함하는 것도 가능하다. 셀렉터를 통해 선택하기 위해서는 리소스가 모든 기준을 만족해야 한다.

---

## 레이블 셀렉터를 이용한 파드 스케줄링 제한

쿠버네티스는 모든 노드를 하나의 대규모 배포 플랫폼으로 여기기 때문에 파드가 어느 노드에 스케줄링 됐는가는 중요하지 않다. 각 파드는 요청한 만큼의 정확한 리소스(CPU, 메모리)를 할당 받는다.

그러나 간혹 스케줄링할 위치를 결정할 필요가 있을 수 있다. 예를 들어 워커 노드가 HDD나 SDD를 가지고 있는 경우, 특정 파드를 둘 중 하나로 선택해 스케줄링 할 수 있는 케이스가 있다. 또한 GPU 가속을 제공하는 노드에만 GPU 계산이 필요한 파드를 스케줄링 할 수 있다.

그러나 이러한 방법은 애플리케이션이 인프라스트럭쳐와 결합되는 요소여서 쿠버네티스의 철학에 썩 좋은 방법이라고는 할 수 없다. 정확한 노드를 지정하는 대신 필요한 노드 요구사항을 미리 기술하여 쿠버네티스가 요구 사항을 만족하는 노드를 선택하도록 한다. 이를 노드 레이블, 레이블 셀렉터를 통해 실현할 수 있다.

### 레이블을 사용한 워커 노드의 분류

노드를 포함한 모든 쿠버네티스 오브젝트에는 레이블링을 할 수 있다. 클러스터에 GPU를 가지고 있는 노드가 있다고 가정한다. 이를 가지고 있음을 레이블을 통해 노출할 수 있다.

```bash
> kubectl label node first-kind-multi-node-worker2 gpu=true
```

### 특정 노드에 파드 스케줄링

아래 manifest는 GPU를 필요로 하는 새로운 파드 배포를 가정한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubia-gpu
spec:
  nodeSelector:
    gpu: "true" # gpu=true 레이블을 포함한 노드에 이 파드를 배포한다.
  containers:
    - image: museopkim/kubia
      name: kubia
```

```bash
> kubectl create -f museop-kubia-gpu.yaml
```

### 하나의 특정 노드에 스케줄링

각 노드에는 키를 [kubernetes.io/hostname](http://kubernetes.io/hostname), 값에 호스트 이름을 설정한 고유한 레이블이 있기 때문에 파드를 특정한 노드로 스케줄링 하는 것도 가능하다. 그러나 nodeSelector에 실제 호스트 이름을 지정 할 경우 만약 해당 노드가 오프 상태라면 파드가 스케줄링 되지 않을 수도 있다.

노드는 개별 노드를 컨트롤 하기 보다는 레이블 셀렉터를 통해 지정한 특정 기준을 만족하는 노드의 논리적인 그룹을 다루는 것이 올바른 방향이다.

---

## 파드에 애노테이션 부여하기

파드 및 다른 오브젝트는 레이블 외에 애노테이션을 가질 수 있다. 애노테이션은 키 값 쌍으로 레이블과 비슷한 성격을 가지고 있지만 식별 정보를 갖지 않는다. 즉, 레이블은 오브젝트를 그룹화 하는데 사용할 수 있지만 애노테이션은 그렇게 할 수 없다. 또한 셀렉터도 존재하지 않는다.

애노테이션은 쿠버네티스에 새로운 기능을 추가할 때 주로 사용된다. 또한 파드나 다른 오브젝트에 설명을 추가할 때 유용하게 사용할 수 있다.

### 애노테이션 추가 및 수정

```bash
> kubectl annotate pod museop-kubia-manual museop.com/someannotation="foo bar"
```

### 애노테이션 조회

```bash
> kubectl describe pod museop-kubia-manual
```

---

## 네임스페이스를 사용한 리소스 그룹화

쿠버네티스 네임스페이스는 오브젝트 이름의 범위를 제공한다. 모든 리소스를 하나의 단일 네임스페이스에 둘 수 있고, 여러 네임스페이스로 분류를 할 수도 있다. 이렇게 분리된 네임스페이스는 같은 리소스 이름을 다른 네임스페이스에 걸쳐 여러번 사용할 수 있게 해준다.

대부분의 리소스 유형은 네임스페이스 안에 속할 수 있지만, 노드는 그렇지 않다. 노드는 전역이며 단일 네임스페이스에 얽매이지 않는다.

### 네임스페이스 조회

```bash
> kubectl get ns
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd354cba-c43f-43b3-b54e-c806b00d0d1f/Untitled.png)

네임스페이스를 명시적으로 지정하지 않으면 kubectl 명령어는 항상 기본적으로 default 네임스페이스에 속해 있는 오브젝트만 표시한다.

### 네임스페이스 생성

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: custom-namespace
```

### 특정 네임스페이스 안에 리소스 만들기

```bash
> kubectl create -f museop-kubia-yaml -n custom-namespace
```

---

## 파드 중지, 제거

### 이름으로 파드 삭제

```bash
> kubectl delete pod museop-kubia-gpu
```

### 레이블 셀렉터를 이용한 파드 삭제

```bash
> kubectl delete pod -l creation_method=manual
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/acec1a12-68b2-4073-990d-fb6270f029c8/Untitled.png)

### 네임스페이스 전체 삭제

```bash
> kubectl delete ns custom-namespace
```

### 네임스페이스를 유지 하면서 내부의 모든 파드 삭제

```bash
> kubectl delete pods --all
```
