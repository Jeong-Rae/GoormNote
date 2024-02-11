# [K8s] CKA 실습 - ReplicaSet

[Kubernetes ReplicaSet 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset/)  
[KodeKloud RepolicaSet practice](https://kodekloud.com/topic/practice-test-replicasets-2/)

## Task 01

> How many PODs exist on the system?  
> In the current(default) namespace.

Pod를 조회하는 기능은 `kubectl get pod`을 통하여 확인할 수 있다.

## Task 02

> How many ReplicaSets exist on the system?  
> In the current(default) namespace.

`kubectl get rs`로 조회할 수 있다.

## Task 03

> How about now? How many ReplicaSets do you see?  
> We just made a few changes!

Task 02와 동일하게 조회가능하다.

## Task 04

> How many PODs are DESIRED in the `new-replica-set`?

`kubectl get rs`에서 `desired` 칼럼을 확인한다.

## Task 05

> What is the image used to create the pods in the `new-replica-set`?

`kubectl describe rs new-replica-set | grep -i image`로 이미지를 조회한다.

## Task 06

> How many PODs are READY in the `new-replica-set`?

`kubectl get rs`에서 `ready` 칼럼을 확인한다.

## Task 07

> Why do you think the PODs are not ready?

`kubectl describe pod new-replica-set-{hash}` 로 pod 정보를 확인한다. `Error: ErrImagePull`로 이미지에 문제가 있는 것으로 보인다. 다음 오류 메시지를 확인하면, 현재 레포지토리에서 해당 이미지를 찾지 못하여 참조하지 못하는 것으로 보인다.

```
Failed to pull image "busybox777": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/busybox777:latest": failed to resolve reference "docker.io/library/busybox777:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
```

## Task 08

> Delete any one of the 4 PODs.

## Task 09

> How many PODs exist now?

`kubectl get pod`으로 조회 가능하다.  
ReplicaSet은 pod의 개수를 항상 일정히 유지하므로, 기존과 같이 4개일 것이라는 예상이 가능하다.

## Task 10

> Why are there still 4 PODs, even after you deleted one?

ReplicaSet은 pod의 개수를 항상 일정히 유지하기 때문이다.

## Task 11

> Create a ReplicaSet using the `replicaset-definition-1.yaml` file located at `/root/`.

`kubectl apply -f replicaset-definition-1.yaml --dry-run=client` 으로 생성을 시도한다.

```
error: resource mapping not found for name: "replicaset-1" namespace: "" from "replicaset-definition-1.yaml": no matches for kind "ReplicaSet" in version "v1"
ensure CRDs are installed first
```

api 버전에 문제가 있는 것으로 보인다. 레플리카셋은 `apiVersion: apps/v1`을 사용해야 한다.

## Task 12

> Fix the issue in the `replicaset-definition-2.yaml` file and create a `ReplicaSet` using it.  
> This file is located at `/root/`.

`kubectl apply -f replicaset-definition-2.yaml --dry-run=client`으로 생성을 시도한다. 문제없이 된다.  
`kubectl apply -f replicaset-definition-2.yaml`로 실제로 생성한다.

```
The ReplicaSet "replicaset-2" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"tier":"nginx"}: `selector` does not match template `labels`
```

template에 있는 label과 matchLabels에 있는 label이 다르다. 둘을 동일하게 `nginx`로 맞춰준다.

## Task 13

> Delete the two newly created ReplicaSets -` replicaset-1` and `replicaset-2`

`kubectl delete -f replicaset-definition-1.yaml`, `kubectl delete -f replicaset-definition-2.yaml`로 지워준다.  
`kubectl get rs`로 name을 확인하고 `kubectl delete rs {rs.name}`로 지우는 것도 가능하다.

## Task 14

> Fix the original replica set `new-replica-set` to use the correct busybox image.  
> Either delete and recreate the ReplicaSet or Update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.

`kubectl set image replicaset/new-replica-set busybox-container=busybox`로 이미지를 변경한다.  
이후 `kubectl delete pod --all`로 이전 pod를 전부 제거한다.

## Task 15

> Scale the ReplicaSet to 5 PODs.  
> Use `kubectl scale` command or edit the replicaset using k`ubectl edit replicaset`.

`kubectl scale rs new-replica-set --replicas=5`를 사용하여 레플리카 개수를 5개로 변경한다.

## Task 16

> Now scale the ReplicaSet down to 2 PODs.  
> Use `kubectl scale` command or edit the replicaset using k`ubectl edit replicaset`.

`kubectl scale rs new-replica-set --replicas=2`를 사용하여 레플리카 개수를 5개로 변경한다.
