# Static PODs

[Kubernetes Static PODs 공식문서 lang.ko](https://kubernetes.io/ko/docs/tasks/configure-pod-container/static-pod/)  
[KodeKloud Static PODs practice](https://kodekloud.com/topic/practice-test-static-pods-2/)

## Task 01

> How many static pods exist in this cluster in all namespaces?

현재 존재하는 Node가 어떤것이 있는지를 확인해야 한다. StaticPod는 pod name 뒤에 node 명이 붙어있다.
`kubectl get node`로 확인된 node이름을 `kubectl get pod --all-namespaces -o wide | grep '\-{node}'`로 선택하여 조회한다.

## Task 02

> Which of the below components is NOT deployed as a static pod?

`kubectl get pod --all-namespaces -o wide`로 조회하였을 때 static pod가 아닌 것을 고른다.

## Task 03

Task 02와 동일

## Task 04

> On which nodes are the static pods created currently?

`kubectl get pod --all-namespaces -o wide`로 조회하였을 때 `-controlplane`만 있는것을 확인할 수있다.

## Task 05

> What is the path of the directory holding the static pod definition files?

`/etc/kubernetes/manifests`에 정의되어 있다.

## Task 06

> How many pod definition files are present in the manifests directory?

`ls /etc/kubernetes/manifests/`로 조회하면 존재한는 설정 file을 확인할 수 있다.

## Task 07

> What is the docker image used to deploy the kube-api server as a static pod?

`kubectl get pod --all-namespaces -o wide`을 통하여 kube-api server에 대한 pod 기본 정보를 조회한다.
namespace가 `kube-system`이고, pod이름이 `kube-apiserver-controlplane`인것을 확인할 수 있다.  
따라서 이미지 조회는 `kubectl describe pod kube-apiserver-controlplane -n=kube-system | grep -i image`를 통하여 할 수 있다.

## Task 08

> Create a static pod named `static-busybox` that uses the `busybox` image and the command `sleep 1000`

`kubectl run static-busybox --image=busybox --dry-run=client -o yaml --command -- "sleep 1000" > busybox.yaml`을 통하여 기본 이미지를 생성한다.  
이후 `cp busybox.yaml /etc/kubernetes/manifests/`로 파일을 복사한다.

## Task 09

> Edit the image on the static pod to use `busybox:1.28.4`

`vim /etc/kubernetes/manifests/busybox.yaml`으로 `Image`필드를 문제에 맞게 수정한다.

## Task 10

> We just created a new static pod named static-greenbox. Find it and delete it.  
> This question is a bit tricky. But if you use the knowledge you gained in the previous questions in this lab, you should be able to find the answer to it.

`kubectl get pod --all-namespaces -o wide`를 통해 `static-greenbox`의 기본정보를 확인한다. `node01`에 위치한 것을 볼 수 있다. 다른 Node에 접속하여 manifest file를 제거해야한다.

`ssh node01`로 다른 노드에 접속한다.

kubelet이 어디서 static pod를 가져오는지 확인하려면 kubelet의 config file을 확인해야한다. 이는 `ps -ef | grep --kubelet`으로 확인할 수 있다.

`--config=/var/lib/kubelet/config.yaml`인 부분을 확인할 수 있다.  
`cat /var/lib/kubelet/config.yaml | grep -i staticpod`
이 결과 `staticPodPath: /etc/just-to-mess-with-you`를 확인할 수 있다.

`ls /etc/just-to-mess-with-you`로 내용을 확인 후 `rm /etc/just-to-mess-with-you/greenbox.yaml`로 manifest file을 제거한다.
