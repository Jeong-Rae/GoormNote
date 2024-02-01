# Node Affinity

[Kubernetes Affinity 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/assign-pod-node/)  
[KodeKloud Affinity practice](https://kodekloud.com/topic/practice-test-node-affinity-3/)

## Task 01

> How many Labels exist on node node01?

`kubectl get node node01 --show-labels` 또는 `kubectl describe node node01`을 통해 확인해볼수 있다.

## Task 02

> What is the value set to the label key `beta.kubernetes.io/arch` on `node01?`

`kubectl get node node01 --show-labels` 또는 `kubectl describe node node01`을 통해 확인해볼수 있다.

## Task 03

> Apply a label `color=blue` to node `node01`

`kubectl label node node01 color=blue`로 새 label을 추가할 수 있다.  
또는 `kubectl edit node node01 -o yaml`을 통해 Labels 필드에 `color: blue`를 직접 추가할 수 있다.

## Task 04

> Create a new deployment named `blue` with the `nginx` image and 3 replicas.

`kubectl create deploy blue --image=nginx -r 3` 을 사용하여 생성할 수 있다.

## Task 05

> Which nodes `can` the pods for the `blue` deployment be placed on?  
> Make sure to check taints on both nodes!

`kubectl get pod -o wide`으로 우선 pod이 할당된 node를 확인할 수 있다.

추가적으로 놓일 수 없다는 것은 taint가 있어야한다. (생성한 deploy에는 toleration이 없다.)
`kubectl describe node | grep Taint`를 하였을 때 두 node에 모두 taint가 없으므로, 두 node에 pod이 스케쥴링될 수 있다.

## Task 06

> Set Node Affinity to the deployment to place the pods on `node01` only.

`kubectl edit deploy blue -o yaml`에서 직접
다음 설정을 추가할 수 있다. 추가 위치는 `spec.template.spec`에 위치한다.  
또는 `kubectl get deploy blue -o yaml > blue.yaml`로 설정을 추출후 아래 설정을 추가하고 `kubectl  apply -f blue.yaml`로 패치를 할 수 있다.

```yaml
affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
                - matchExpressions:
                      - key: color
                        operator: In
                        values:
                            - blue
```

## Task 07

> Which nodes are the pods placed on now?

blue에서 만드는 pod은 `color=blue`가 설정되어 있어야 하므로 node01에만 할당 될 수 있을 것이다.

## Task 08

> Create a new deployment named `red` with the `nginx` image and `2` replicas, and ensure it gets placed on the `controlplane` node only.  
> Use the label key - `node-role.kubernetes.io/control-plane` - which is already set on the `controlplane` node.

`kubectl create deploy red --image=nginx -r 2 --dry-run=client -o yaml > red.yaml` 로 기본 deploy 생성 파일을 만들었다.
이후 `spec.template.spec`에 다음 설정을 추가한다.

```yaml
affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
                - matchExpressions:
                      - key: node-role.kubernetes.io/control-plane
                  operator: Exists
```
