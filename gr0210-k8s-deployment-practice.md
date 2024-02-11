# [K8s] CKA 실습 - Deployment

[Kubernetes Deployment 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/)  
[KodeKloud Deployment practice](https://kodekloud.com/topic/practice-tests-deployments-2/)

## Task 01

> How many PODs exist on the system?  
> In the current(default) namespace.

`kubectl get pod`으로 조회할 수 있다.

## Task 02

> How many ReplicaSets exist on the system?
> In the current(default) namespace.

`kubectl get rs`로 조회할 수 있다.

## Task 03

> How many Deployments exist on the system?  
> In the current(default) namespace.

`kubectl get deploy`로 조회할 수 있다.

## Task 04

> How many Deployments exist on the system now?  
> We just created a Deployment! Check again!

Task 03과 같이 조회할 수 있다.

## Task 05

> How many ReplicaSets exist on the system now?

`kubectl get rs`로 다시 조회할 수 있다.

## Task 06

> How many PODs exist on the system now?

`kubectl get pod`로 다시 조회할 수 있다.

## Task 07

> Out of all the existing PODs, how many are ready?

`kubectl get deploy`에서 `READY` 칼럼을 확인한다.

## Task 08

> What is the image used to create the pods in the new deployment?

`kubectl describe deploy frontend-deployment`에서 템플릿을 확인하거나,  
`kubectl describe pod frontend-deployment-{hash} | grep -i image`로 확인할 수 있다.

## Task 09

> Why do you think the deployment is not ready?

`kubectl describe pod {pod.name}`에서 이벤트를 확인한 결과, 이미지를 찾지 못하는 문제가 있다.

## Task 10

> Create a new Deployment using the `deployment-definition-1.yaml` file located at `/root/`.  
> There is an issue with the file, so try to fix it.

`kubectl apply -f deployment-definition-1.yaml --dry-run=client`으로 생성을 시도해본다. 문제 없는 것을 알 수있다.

`kubectl apply -f deployment-definition-1.yaml`로 실제 생성을 시도한다.

```
Error from server (BadRequest): error when creating "deployment-definition-1.yaml": deployment in version "v1" cannot be handled as a Deployment: no kind "deployment" is registered for version "apps/v1" in scheme "k8s.io/apimachinery@v1.27.1-k3s1/pkg/runtime/scheme.go:100"
```

`deployment`라는 api를 찾지 못한다. 올바른 예시는 공식문서대로 `kind: Deployment`로 대문자로 작성되어야 한다.

## Task 11

> Create a new Deployment with the below attributes using your own deployment definition file.  
> Name: `httpd-frontend`;  
> Replicas: `3`;  
> Image: `httpd:2.4-alpine`

`kubectl create deploy httpd-frontend --image=httpd:2.4-alpine --replicas=3`를 사용하여 생성한다.
