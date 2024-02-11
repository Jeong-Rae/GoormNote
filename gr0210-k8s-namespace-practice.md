# [K8s] CKA 실습 - Namespace

[Kubernetes Namespace 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/namespaces/)  
[KodeKloud Namespace practice](https://kodekloud.com/topic/practice-test-namespaces-2/)

## Task 01

> How many Namespaces exist on the system?

`kubectl get ns`로 확인할 수 있다.  
개수만 필요하다면 `kubectl get ns | wc -l`를 활용할 수 있다.

## Task 02

> How many pods exist in the research `namespace`?

`kubectl get pod -n=research`를 사용하여 조회할 수 있다. `-n`옵션으로 k8s의 리소스들의 네임스페이스를 지정할 수 있다.

## Task 03

> Create a POD in the `finance` namespace.  
> Use the spec given below.

`kubectl run redis --image=redis -n=finance`를 사용해 생성한다.

## Task 04

> Which namespace has the `blue` pod in it?

`kubectl get pod --all-namespaces | grep -i blue`로 모든 pod중 `blue`가 속한 namespace를 조회한다.

## Task 05

> Access the Blue web application using the link above your terminal!!  
> From the UI you can ping other services.

## Task 06

> What DNS name should the `Blue` application use to access the database `db-service` in its own namespace - `marketing`?
> You can try it in the web application UI. Use port `6379`.

`kubectl get all -n=marketing`를 사용하여 `marketing`에 위치한 k8s 리소스들을 확인할 수 있다. `service/db-service`와 `pod/blue`는 같은 namespace에 위치해 있다. 따라서 `db-service`를 사용하여 DNS에 접근가능하다.

## Task 07

> What DNS name should the `Blue` application use to access the database `db-service` in the `dev` namespace?
> You can try it in the web application UI. Use port `6379`.

`marketing`와 `dev`라는 다른 namespace에 위치해있다. 이러한 서로다른 네임스페이스에서 DNS를 참조할 때는 `{name}.{namespace}.svc.cluster.local`을 사용해 참조해야한다.
