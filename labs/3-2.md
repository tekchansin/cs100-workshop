# Kubernetes integrate Ceph as a Storage Class
## Install ceph client
- ceph-common

## Setup K8s
### Install microk8s on ubuntu
- snap install microk8s --classic
- snap alias microk8s.helm helm
- snap alias microk8s.kubectl kubectl
### Start k8s service
- microk8s.status

### k8s status
- microk8s.status

### enable k8s helm
- microk8s.enable helm


## Config Ceph Pool for K8S
### Create Pool and Auth
- ceph osd pool create kube 8
- ceph auth get-or-create client.kube mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=kube' -o ceph.client.kube.keyring

### Get Admin and User keyring base64
- ceph auth get-key client.admin | base64
- ceph auth get-key client.kube | base64

## Config RBD driver for K8S


### Apply secret
- Copy based64-secret to admin-secret.yml and user-secret.yml
- kubectl apply -f ./k8s/admin-secret.yml
- kubectl apply -f ./k8s/user-secret.yml

### Apply storageclass
- kubectl apply -f ./k8s/storageclass.yml

### Create PVC
- kubectl apply -f ./k8s/pvc.yml

### Check pvc status
- kubectl get pvc  (status should be Bound)

### Create a container(pod) with mount rbd image
- kubectl apply -f ./k8s/po.yml

### Check pod status
- kubectl get pod
- kubectl describe pod/ceph-pod1

### Check moutpath in container
- kubectl exec -it ceph-pod1 sh
- df -h

### Check image on Ceph Pool
- rbd info -p kube <image-id>