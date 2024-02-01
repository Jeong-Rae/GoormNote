# PODs

[Kubernetes PODs 공식문서 lang.ko](https://kubernetes.io/ko/docs/concepts/workloads/pods/)  
[KodeKloud PODs practice](https://kodekloud.com/topic/practice-test-pods-2/)

## Task 01

> How many `pods` exist on the system?  
> In the current(default) namespace.

Pod를 조회하는 기능은 `kubectl get pod`을 통하여 확인할 수 있다.
`No resources found in default namespace.`

## Task 02

> Create a new pod with the `nginx` image.

Pod을 특정 이미지를 사용하여 생성하는 것은 `kubectl run` 을 사용할 수 있다.
`kubectl run {name} --image=nginx`를 통하여 생성 이미지도 지정할 수 있다.

## Task 03

> T

## Task 04

> What is the image used to create the new pods?  
> You must look at one of the new pods in detail to figure this out.

Pod의 이미지를 비롯한 세부사항을 확인하는 명령어는 `kubectl describe pod`을 사용할 수 있다.

`kubectl describe pod newpods-rqrsr | grep Image` 를 통하여 Image 정보만 확인할 수 있었다.

## Task 05

> Which nodes are these pods placed on?  
> You must look at all the pods in detail to figure this out.

Node에 대한 정보는 `kubectl get pod -o wide`를 통하여 간단하게 확인해 볼 수 있다. 또는 `kubectl describe pod newpods-rqrsr | grep Node`을 통하여 Node에 관한 정보만 추출해서 확인하는 것도 가능하였다.

## Task 06

> How many containers are part of the pod `webapp`?  
> Note: We just created a new POD. Ignore the state of the POD for now.

pod 아래 만들어진 컨테이너의 개수는 `kubectl get pod`에서 간단하게 확인할 수 있다. 자세한 정소는 역시 `kubectl describe pod`으로 확인할 수 있다.

## Task 07

> What images are used in the new webapp pod?  
> You must look at all the pods in detail to figure this out.

자세한 이미지 정보가 필요한 상황이므로 `kubectl describe pod`을 사용한다. `kubectl describe pod webapp | grep Image`으로 Image 정보들만 추출할 수 있다.

## Task 08

> What is the state of the container `agentx` in the pod `webapp`?  
> Wait for it to finish the `ContainerCreating` state

`State`에 대한 정보 역시 `kubectl describe pod`으로 조회 가능하다.  
보기 쉽게 확인하기 위하여 `grep State`를 추가하는 경우 state에 대한 정보는 나오지만 어떤 컨테이너에 대한 상태인지를 알 수 없기에 추가적인 조회가 필요하여 크게 의미가 없었던 것 같다.

## Task 09

> Why do you think the container `agentx` in pod `webapp` is in error?  
> Try to figure it out from the events section of the pod.

`kubectl describe pod webapp`를 실행한 결과가 다음과 같았다.

```shell
Warning  Failed     12m (x3 over 13m)     kubelet            Error: ErrImagePull
Warning  Failed     12m (x5 over 13m)     kubelet            Error: ImagePullBackOff
Normal   Pulling    11m (x4 over 13m)     kubelet            Pulling image "agentx"
Warning  Failed     11m (x4 over 13m)     kubelet            Failed to pull image "agentx": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/agentx:latest": failed to resolve reference "docker.io/library/agentx:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
Normal   BackOff    3m20s (x42 over 13m)  kubelet            Back-off pulling image "agentx
```

지속적으로 `ImagePull`에서 문제가 생기는 상황임을 알 수 있다. 상세로그에서는 이미지를 찾을 수 없거나, 접근을 못하고 있는 상홤인 것을 추가적으로 확인할 수 있다.

## Task 10

> What does the `READY` column in the output of the `kubectl get pods` command indicate?

Running 상태의 컨테이너와, 전체 컨테이너의 상태를 제공하는 것으로 보는 것이 옳다.

## Task 11

> Delete the `webapp` Pod.  
> Once deleted, wait for the pod to fully terminate.

pod 제거는 `kubectl delete pod `을 이용할 수 있다.

## Task 12

> Create a new pod with the name `redis` and the image `redis123`.  
> Use a pod-definition YAML file. And yes the image name is wrong!

yaml을 통하여 k8s 컴포넌트를 생성할 때는 `kubectl create -f {file}` 또는 `kubectl apply -f {file}`을 사용할 수 있다.

이때 사용될 yaml은 공식문서에 있는 pod 기본 yaml을 변경하여 사용한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: redis
spec:
    containers:
        - name: redis
          image: redis123
```

또는 명령줄 커맨드를 사용하여 yaml로 export 시키는 방법또한 있다.
`kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml`을 사용하여 yaml을 저장하는 것이다.  
`--dry-run=client`은 실제로 api서버에는 생성하지 않고, 올바른 설정인지 여부만 판단하는 옵션이다.  
`-o yaml`은 해당 설정에 맞는 yaml 형식을 출력하는 옵션이다.
이렇게 저장된 `redis.yaml`을 `kubectl create -f redis.yaml`을 사용해 pod을 생성한다.

## Task 13

> Now change the image on this pod to `redis`.  
> Once done, the pod should be in a running state.

`kubectl describe pod redis`를 확인하면 `ImagePull`에서 문제가 있는 것을 확인할 수 있다.
`redis123`이라는 이미지를 `redis`로 변경하여 이 문제를 해결해야한다.

이전에 작성한 `redis.yaml`에서 image에 해당하는 부분을 `redis`로 수정하고 `kubectl apply -f redis.yaml`을 사용하여 수정한다.  
`apply`는 기존의 yaml에서 변경된 사항을 수정하여 반영해주는 기능을 수행한다.

또는 `kubectl edit pod redis -o yaml`로 pod의 정보를 직접 수정할 수도 있다. `-o yaml`이 없을 경우 json 형식으로 출력될 수 있다. 이경우에는 원하는 필드를 찾기가 어려울 수 있다.
