# CKA Environment

Create a Kubernetes environment for learning CKA.

## Introduction

This config can be is used for experimenting with Kubernetes.
You need Virtual Box and Vagrant for the installation of the virtual machines
https://www.vagrantup.com/downloads.html
https://www.virtualbox.org/wiki/Downloads

## Installing the Boxes

To install the boxes, do the following:

```console
vagrant box add ubuntu/bionic64
==> box: Loading metadata for box 'ubuntu/bionic64'
    box: URL: https://vagrantcloud.com/ubuntu/bionic64
This box can work with multiple providers! The providers that it
can work with are listed below. Please review the list and choose
the provider you will be working with.

1) hyperv
2) libvirt
3) virtualbox
4) vmware_desktop

Choose 3

git clone https://github.com/DennisVermeulen/cka.git

cd cka/k8smaster
vagrant up
cd ../k8sworker
vagrant up
cd ../k8smaster

vagrant ssh

# Bootstrap 

sudo su -
kubeadm init --config=kubeadm-config.yaml --upload-certs | tee kubeadm-init.out

# Copy config file to communicate with the cluster

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Deploy a pod network to the cluster

kubectl create -f calico.yaml

# Check running pods

root@k8smaster:~# kubectl -n kube-system get pods

NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-6b9d4c8765-p9wn7   1/1     Running   0          52s
calico-node-bgklp                          1/1     Running   0          52s
coredns-5644d7b6d9-fmbsb                   1/1     Running   0          71s
coredns-5644d7b6d9-tdn9f                   1/1     Running   0          71s
etcd-k8smaster                             1/1     Running   0          13s
kube-apiserver-k8smaster                   1/1     Running   0          30s
kube-controller-manager-k8smaster          1/1     Running   0          19s
kube-proxy-kknp8                           1/1     Running   0          71s
kube-scheduler-k8smaster                   1/1     Running   0          32s


# Join any number of worker nodes by running the following on each as root:

cd ../k8sworker
vagrant ssh
sudo -i 

# Get the following information out of the kubeadm-init.out for joining the worker in the cluster.
kubeadm join k8smaster:6443 --token pzybeg.5ebv6ct0ts7q0dde \
    --discovery-token-ca-cert-hash sha256:b8bac2cb45f9e1e1c9db53dded9be20b3304462dc2586fe7ad5418e034013a58 
```
