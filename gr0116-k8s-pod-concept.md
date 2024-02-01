# PODs

[Kubernetes PODs 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/workloads/pods/)

## POD란

`Docker` 또는 `ContainerD`와 같은 `Container Runtime`을 사용하여 특정 리소스 환경을 격리시키는 `Container(컨테이너)`라는 개념이 있었다. k8s에서는 이러한 컨테이너들을 `pod(파드)` 라는 집합으로 묶는 객체가 존재한다.
컨테이너라는 개념을 pod라는 개념으로 wrapping 하여 k8s의 내부시스템으로 편입시키는 것이다.  
즉 pod는 특정 process를 지칭하는 것이 아니라, 특정 process를 위한 환경을 의미하는 것이다.

## POD 관리

### 내부 네트워크

Pod들간에는 NAT Gateway가 존재하지 않는다. 즉 pod간의 직접적인 통신이 가능하며, 유일한 PrivateIP를 발급받음을 의미한다.  
이 IP는 pod 내부의 컨테이너에게도 상속된다. 즉 10.1.1.1이라는 IP를 가지는 pod에서 8080 포트에서 실행되는 컨테이너는 10.1.1.1:8080으로 연결된다. 이 경우 pod 내부의 컨테이너(process)들은 localhost를 통하여 서로를 참조할 수 있다는 것도 의미한다.

### 내부 저장소

Pod 내부에는 pod의 있는 모든 process들이 접근 가능한 내부 저장소가 존재한다. 해당 저장소는 Node 리소스의 일부를 격리시켜 사용한다.

## LifeCycle

### Pod의 단계

Pod는 다음과 같은 `phase`를 가지고 있다.

-   `Pending`
    > Pod 생성에 대한 승인이 이루어졌다. 하지만 아직 컨테이너가 만들어 지지 않아 실행되지 않은 상태를 의미한다.
-   `Running`
    > Pod가 Node에 생성되었다. 하나이상의 컨테이너가 실행, 또는 시작중에 있다.
-   `Succeeded`
    > Pod 내의 모든 컨테이너가 정상적으로 종료되었다. 재시작은 이루어지지 않는다.
-   `Failed`
    > Pod내의 모든 컨테이너가 종료되었다. 일부 컨테이너가 실패로 종료되었다.
-   `Unknown`
    > Pod에 대한 정보를 얻을 수 없다. 주로 api서버와 Node의 통신오류가 원인이 된다.

### 컨테이너 상태

Pod내부의 Container는 다음과 같은 `status`를 가진다.

-   `Running`
    > 컨테이너가 아무 문제 없이 실행되고 있는 상태
-   `Terminated`
    > 컨테이너가 종료된 상태 또는 실패한 상태
-   `Waiting`

    > Running과 Terminated가 아닌 상태. 컨테이너가 실행되기 이전 여러 리소스를 준비하는 과정을 포함한다.

### Pod 컨디션

Pod는 `PodStatus`를 가지며, 이 status들을 `PodConditions`로 묶어서 관리한다. `PodStatus`는 다음과 같은 종류가 있다.

-   `PodScheduled`
    > Pod가 Node에 할당 되었는지에 대한 여부이다.
-   `PodHasNetwork`
    > Pod의 샌드박스와 네트워크가 구성되었는지에 대한 여부이다.
-   `ContainersReady`
    > Pod의 모든 컨테이너가 준비되었는지 나타낸다
-   `Initialized`
    > Pod의 모든 initial 컨테이너가 준비되었는지를 나타낸다.
-   `Ready`
    > 사용가능한 Pod의 상태이다. 서비스에 등록이 된 상태이다.

### 컨테이너 프로브

위 사항들을 보면 K8s의 Pod에서 실행되는 Container들의 상태에 대한 정보를 알고있어야 한다. 이러한 정보를 얻기 위해 kubelet은 다양한 방법으로 컨테이너의 상태를 진단한다.

상태진단을 위해서 `exec`로 직접 확인하기, `httpGet`요청을 통하여 응답받기 등의 방법을 사용한다.

`프로브 종류`는 실행 여부를 나타내는 `livenessProbe`와, 요청 준비 상태를 검증하는 `readinessProbe`, 컨테이너의 app이 실행되었는지를 검증하는 `startupProbe`가 있다.

-   `livenessProbe`는 컨테이너가 응답하지 않는 비정상상태일 때 종료시키고, 재시작할 수 있다.

-   `readinessProbe`는 내부 app이 실행은 되었으나 요청을 처리하기 어려운 경우 사용할 수 있다. 해당 설정이 되어있는 동안 외부 트래픽을 처리하지 않는다.

-   `startupProbe`는 컨테이너 내부의 app이 구동하는데 오랜 시간이 걸리는 것이 예상되는 경우 사용할 수 있다. app의 구동보다 다른 probe의 실행이 더 빠르다면 비정상 컨테이너가 되어 종료된다.startupProbe가 설정되면 다른 probe는 대기상태가 되고, startupProbe가 종료된 뒤에 검증을 시작한다. 이러한 설정을 통하여 구동이 오래걸리는 app의 probe문제를 해결할 수 있다.

### pod의 생성과 실행

Pod의 실행과정은 다음과 같이 이루어진다.

1. 사용자의 Pod 실행을 요청한다. 해당 요청은 K8s의 `API 서버`로 전송된다.
2. `API 서버`는 해당 요청을 검증하고, `ETCD`에 요청을 기록한다.
3. 정상적인 요청이라면 pod를 생성한다.
4. `Scheduler`는 생성된 pod가 위치할 적절한 `Node`를 찾아 할당한다.
5. 할당된 `Node`의 `Kubelet`은 Pod의 설정에 따라 `Container Runtime`에 컨테이너 생산을 요구한다.
6. `컨테이너 런타임`은 제공된 Template에 맞춰 컨테이너를 생성한다.
7. 만들어진 컨테이너와 Pod의 상태는 `Kubelet`이 `API 서버`에 전송한다.
8. Pod가 `Running`상태로 전환된다.

### 파드의 종료

-   Flow
-   GC
