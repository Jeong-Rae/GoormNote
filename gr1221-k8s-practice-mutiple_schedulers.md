## Task 01

> What is the name of the POD that deploys the default kubernetes scheduler in this environment?

기본 스케쥴러와 같은 pod는 `kube-system` ns에 위치하므로 `kubectl get pod -n=kube-system`로 확인한다.

## Task 02

> What is the image used to deploy the kubernetes scheduler?  
> Inspect the kubernetes scheduler pod and identify the image

`kube-system`에 있는 pod의 image정보를 조회해야 한다. `kubectl describe pod kube-scheduler-controlplane -n=kube-system| grep -i image`를 이용한다.

## Task 03

> We have already created the `ServiceAccount` and `ClusterRoleBinding` that our custom scheduler will make use of.

> Checkout the following Kubernetes objects:  
> `ServiceAccount`: my-scheduler (kube-system namespace)  
> `ClusterRoleBinding`: my-scheduler-as-kube-scheduler  
> `ClusterRoleBinding`: my-scheduler-as-volume-scheduler  
> Run the command: kubectl get serviceaccount -n kube-system and kubectl get clusterrolebinding

> Note: - Don't worry if you are not familiar with these resources. We will cover it later on.

## Task 04

> Let's create a configmap that the new scheduler will employ using the concept of `ConfigMap as a volume`.  
> We have already given a configMap definition file called `my-scheduler-configmap.yaml `at `/root/` path that will create a configmap with name `my-scheduler-config` using the content of file `/root/my-scheduler-config.yaml`.

`kubectl apply -f /root/my-scheduler-configmap.yaml`으로 생성하면 된다.

## Task 05


