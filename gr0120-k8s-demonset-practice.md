# DaemonSet

[Kubernetes DaemonSets 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/workloads/controllers/daemonset/)  
[KodeKloud DaemonSet practice](https://kodekloud.com/topic/practice-test-daemonsets-2/)

## Task 01

> How many `DaemonSets` are created in the cluster in all namespaces?  
> Check all namespaces

`kubectl get ds --all-namespaces`로 확인할 수 있다.
개수만 확인하기 위해 `| wc -l`로 파이프를 설정할 수 있다.

## Task 02

> Which namespace is the `kube-proxy` Daemonset created in?

`kubectl get ds --all-namespaces`로 확인하였을 때 `kube-system`에 위치한 것을 알 수 있다.

## Task 03

> Which of the below is a `DaemonSet`?

`kubectl get ds --all-namespaces`로 확인하여 리스트와 선택지를 비교한다.

## Task 04

> On how many nodes are the pods scheduled by the DaemonSet `kube-proxy`?

`kubectl get ds --all-namespaces`로 확인한다. 스케쥴링 되어 있지 않은 노드 하나와, 할당된 노드 한 개가 존재한다.

## Task 05

> What is the image used by the POD deployed by the `kube-flannel-ds` DaemonSet?

이전에 확인하였을 때 `n=kube-flannel`가 설정되어 있는 것을 보았다. 따라서 조회할 때는 `kubectl describe ds kube-flannel-ds -n=kube-flannel`를 사용한다.

image를 확인할 것이므로 `kubectl describe ds kube-flannel-ds -n=kube-flannel | grep -i image`

## Task 06

> Deploy a DaemonSet for `FluentD `Logging.  
> Use the given specifications.

공식문서에서 `Daemonset`에 대한 기본 설정 파일을 가져와 수정한다.

`Name`과 관련된 부분과 `Image`에 대한 부분을 적절히 수정한다. `metadata`의 `namespace`도 `kube-system`으로 작성해야한다.


