# Taints and Tolerations

[Kubernetes Taints and Tolerations 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/taint-and-toleration/)  
[KodeKloud Taints and Tolerations practice](https://kodekloud.com/topic/practice-test-resource-limits-2/)

## Task 01

> How many `nodes` exist on the system?  
> Including the `controlplane` node.

`kubectl get node`를 통하여 확인할 수 있다.

## Task 02

> Do any taints exist on `node01` node?

`kubectl describe node node01`로 확인할 수 있다.
`kubectl describe node node01 | grep Taint`로 해당 항목만 확인하였다.

## Task 03

> Create a taint on `node01` with key of `spray`, value of `mortein` and effect of `NoSchedule`

`kubectl taint node` 명령어로 Node에 Taint를 추가할 수 있다.

설정은 `spray=mortein:NoSchedule`로 가능하다.

즉 다음 명령어가 해당 기능을 수행한다. `kubectl taint node node01 spray=mortein:NoSchedule`

## Task 04

> Create a new pod with the `nginx` image and pod name as `mosquito`.

새로운 pod은 `kubectl run`으로 실행가능하다. 이미지와 pod이름을 포함하는 명령어는 `kubectl run mosquito --image=nginx`로 실핼할 수 있다.

## Task 05

> What is the state of the POD?

`kubectl get pod`으로 확인 할 수 있다.

## Task 06

> Why do you think the pod is in a pending state?

pending 되는 원인을 확인하기 위해 `kubectl describe pod`을 통해 확인할 수 있다.

```shell
Events:
  Type     Reason            Age    From               Message
  ----     ------            ----   ----               -------
  Warning  FailedScheduling  2m37s  default-scheduler  0/2 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 1 node(s) had untolerated taint {spray: mortein}. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling..
```

control-plane과 node1에 taint가 있지만, 이를 허용하는 toleration이 없어서 스케쥴링이 되지 못하고 있는 상황임을 알 수 있다.

## Task 07

> Create another pod named `bee` with the `nginx` image, which has a toleration set to the taint `mortein`.

기본적인 pod 생성 yaml을 만든다. `kubectl run bee --image=nginx --dry-run=client -o yaml > bee.yaml`

이후 `spec`에 다음 옵션을 추가한다.

```yaml
tolerations:
    - key: "spray"
      operator: "Equal"
      value: "mortein"
      effect: "NoSchedule"
```

이후 `kubectl apply -f bee.yaml`로 pod를 실행시킨다.

## Task 08

> Do you see any taints on `controlplane` node?

`kubectl describe node controlplane | grep Taint`으로 확인해 볼수 있다.

```shell
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
```

`NoSchedule` Taint가 있는 것을 알 수 있다.

## Task 09

> Remove the taint on `controlplane`, which currently has the taint effect of `NoSchedule`.

Taint 제거는 Taint 생성 명령어 뒤에 `-`를 붙여서 할 수 있다.  
어떤 Taint가 있는지 이전에 확인한 것을 그대로 사용할 수 있다.
`kubectl taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-`

## Task 10

> What is the state of the pod `mosquito` now?

이전 `mosquit`는 controlplane의 taint로 인해 스케쥴링 되지 못했으므로, taint가 제거된 이후 정상적으로 스케쥴링 되엇을 것을 예상할 수있다.

`kubectl get pod`의 결과 `Running`상태임을 알 수 있으며, 위치한 node가 `controlplane`인 것도 알 수 있다.

## Task 11

> Which node is the POD mosquito on now?

`kubectl get pod -o wide` 결과 `controlplane`에 위치한 것을 확인하였다.
