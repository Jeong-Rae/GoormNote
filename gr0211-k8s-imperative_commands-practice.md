# [K8s] CKA 실습 - Imperative Commands

[Kubernetes Imperative Commands 공식문서 lang.en](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)  
[KodeKloudImperative Commands practice](https://kodekloud.com/topic/practice-test-imperative-commands-3//)

## Task 01

> In this lab, you will get hands-on practice with creating Kubernetes objects imperatively.  
> All the questions in this lab can be done imperatively. However, for some questions, you may need to first create the YAML file using imperative methods. You can then modify the YAML according to the need and create the object using `kubectl apply -f` command.

가능한 명령어로 리소스를 생성해 보자. 몇몇 문제의 경우 `-o yaml --dry-run=client`를 사용하여 yaml를 만든후 `vim`으로 수정해야할 수 있다.

## Task 02

> Deploy a pod named `nginx-pod` using the `nginx:alpine` image.  
> Use imperative commands only.

`kubectl run  nginx-pod --image=nginx:alpine`로 생성할 수 있다.  
이미지는 `--image`로 설정할 수 있다.

## Task 03

> Deploy a `redis` pod using the `redis:alpine` image with the labels set to `tier=db`.  
> Either use imperative commands to create the pod with the labels. Or else use imperative commands to generate the pod definition file, then add the labels before creating the pod using the file.

`kubectl run redis --image=redis:alpine --labels tier=db`로 만들 수 있다.  
라벨은 `--labels`로 설정가능하다.

## Task 04

> Create a service `redis-service `to expose the `redis` application within the cluster on port `6379`.  
> Use imperative commands.

`kubectl expose pod redis --port 6379 --name=redis-service`로 서비스를 만들 수 있다. 타입을 따로 지정하지 않으면 default가 clusterIP이다.  
포트는 `--port`, 서비스명은 `--name`으로 설정할 수 있다.

## Task 05

> Create a deployment named `webapp` using the image `kodekloud/webapp-color` with `3` replicas.  
> Try to use imperative commands only. Do not create definition files.

`kubectl create deploy webapp --image=kodekloud/webapp-color --replicas=3`로 디플로이를 만들 수 있다.  
이미지는 `--image`, 레플리카 개수는 `--replicas`로 설정가능하다.

## Task 06

> Create a new pod called `custom-nginx` using the `nginx` image and expose it on `container port 8080`.

`kubectl run custom-nginx --image=nginx --port=8080`로 파드를 만들 수 있다.  
파드의 포트 설정은 `--port`로 할 수 있다.

## Task 07

> Create a new namespace called `dev-ns`.  
> Use imperative commands.

`kubectl create namespace dev-ns`로 네임스페이스를 생성할 수 있다.

## Task 08

> Create a new deployment called `redis-deploy` in the `dev-ns` namespace with the `redis` image. It should have `2` replicas.  
> Use imperative commands.

`kubectl create deployment redis-deploy --image=redis --replicas=2 --namespace=dev-ns `로 만들 수 있다.  
생성되는 리소스의 네임스페이스 지정은 `--namespace`로 할 수 있다.

## Task 09

> Create a pod called `httpd` using the image `httpd:alpine` in the default namespace. Next, create a service of type `ClusterIP `by the same name `(httpd)`. The target port for the service should be `80`.  
> Try to do this with as few steps as possible.

`kubectl run httpd --image=httpd:alpine`로 pod를 생성한다. 이후 `kubectl expose httpd --port 80`로 서비스를 만들어 pod를 expose한다.

`--expose` 옵션을 활용하여 한 줄로 실행 가능한 방법도 제시된다. `kubectl run httpd --image=httpd:alpine --port=80 --expose`

하지만 pod와 service의 역할 및 생성을 분리하여 지정하는 것이 더 올바른 방법이라 생각되어 첫 번째 방법을 사용할 것 같다.
