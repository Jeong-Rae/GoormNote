# Resource Limits

[Kubernetes Resource Limits 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/policy/limit-range//)  
[KodeKloud Resource Limits practice](https://kodekloud.com/topic/practice-test-taints-and-tolerations-2/)

## Task 01

> A pod called `rabbit` is deployed. Identify the CPU requirements set on the Pod  
> in the current(default) namespace

`kubectl describe pod rabbit`을 사용하여 Limits와 Requests필드를 확인할 수 있다.

## Task 02

> Delete the rabbit Pod.  
> Once deleted, wait for the pod to fully terminate.

`kubectl delete pod rabbit`으로 rabbit pod을 제거할 수 있다.

## Task 03

> Another pod called `elephant` has been deployed in the default namespace. It fails to get to a running state. Inspect this pod and identify the `Reason` why it is not running.

`kubectl get pod`을 한 결과 Status가 `OOMKilled`인 것을 볼 수있다.

## Task 04

> The status `OOMKilled` indicates that it is failing because the pod ran out of memory. Identify the memory limit set on the POD.

OK  
`OOMKilled`가 메모리부족으로 발생하는 상태임을 설명한다.

## Task 05

> The `elephant` pod runs a process that consumes 15Mi of memory. Increase the limit of the `elephant` pod to 20Mi.  
> Delete and recreate the pod if required. Do not modify anything other than the required fields.

`kubectl run elephant --image=polinux/stress --dry-run=client -o yaml > elephant.yaml`으로 기본 yaml 설정을 생성한다.
이후 `spec.containers`에 다음 항목을 추가한다.

```yaml
resources:
    requests:
        memory: 20Mi
    limits:
        memory: 20Mi
```

## Task 06

> Inspect the status of POD. Make sure it's running

`kubectl get pod`의 결과 `elephant`의 Status는 `CrashLoopBackOff`임을 확인할 수 있다.

## Task 07

> Delete the elephant Pod.  
> Once deleted, wait for the pod to fully terminate.

이전과 마찬가지로 `kubectl delete pod elephant`로 제거 가능하다.
