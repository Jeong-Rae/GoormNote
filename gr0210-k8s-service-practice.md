# [K8s] CKA 실습 - Service

[Kubernetes Service 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/services-networking/service/)  
[KodeKloud Service practice](https://kodekloud.com/topic/practice-test-services-2/)

## Task 01

> How many Services exist on the system?  
> In the current(default) namespace

`kubectl get svc`로 확인할 수 있다.

## Task 02

> That is a default service created by Kubernetes at launch.

OK

## Task 03

> What is the type of the default `kubernetes` service?

`kubectl get svc`의 `TYPE` 칼럼을 확인하여 알 수 있다.

## Task 04

> What is the `targetPort` configured on the `kubernetes` service?

`kubectl describe svc kubernetes | grep -i port`로 port 정보를 확인할 수 있다.

## Task 05

> How many labels are configured on the `kubernetes` service?

`kubectl describe svc`에서 `Labels` 부분을 확인한다.

## Task 06

> How many Endpoints are attached on the `kubernetes` service?

` kubectl describe svc`에서 `Endpoints` 부분을 확인 할 수 있다.

## Task 07

> How many Deployments exist on the system now?  
> In the current(default) namespace

`kubectl get deploy`로 확인 가능하다.

## Task 08

> What is the image used to create the pods in the deployment?

`kubectl describe deploy simple-webapp-deployment | grep -i image`로 pod spec의 image 정보를 가져올 수 있다.

## Task 09

> Are you able to accesss the Web App UI?  
> Try to access the Web Application UI using the tab `simple-webapp-ui` above the terminal.

사용해보면 접속할 수 없다.

## Task 10

> Create a new service to access the web application using the `service-definition-1.yaml` file.  
> `Name`: webapp-service  
> `Type`: NodePort  
> `targetPort`: 8080  
> `port`: 8080  
> `nodePort`: 30080  
> `selector.name`: simple-webapp

다음과 같이 제공된 파일을 수정하고, `kubectl apply -f service-definition-1.yaml`로 생성할 수 있다.

```yaml
---
apiVersion: v1
kind: Service
metadata:
    name: webapp-service
    namespace: default
spec:
    ports:
        - nodePort: 30080
          port: 8080
          targetPort: 8080
    selector:
        name: simple-webapp
    type: NodePort
```

## Task 11

> Access the web application using the tab `simple-webapp-ui` above the terminal window.

OK
