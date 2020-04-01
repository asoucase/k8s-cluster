# Kubernetes Cluster with Kubeadm, Vagrant, and Vbox

This is a tool I use to play with K8s clusters on my MacOS using Kubeadm, Vagrant, and Virtual Box. 

## Pre-requisites

 * **[Vagrant 2.1.4+](https://www.vagrantup.com)**
 * **[Virtualbox 5.2.18+](https://www.virtualbox.org)**

## Features 

I started from the [following project](https://github.com/ecomm-integration-ballerina/kubernetes-cluster/) that I fixed to: 
 
1. Configure the deploy via YAML file.
2. Support the K8s 1.6 APIs, K8s 1.18, and Calico 3.8.8
3. Automatic K8s dashboard deploy. 
 
## Run the K8s Cluster

Execute the following command to start a new Kubernetes cluster. Default configuration will start one master (called ```k8s-head```) and two workers (called ```k8s-node-1``` and ```k8s-node-2```).

```
git clone https://github.com/sasadangelo/k8s-cluster
cd k8s-cluster
vagrant up
```

## How to change the configuration

By default, the cluster is created with three nodes, one master (called ```k8s-master```) and two workers (called ```k8s-worker-1``` and ```k8s-worker-2```). You can change the number of servers, their names, and other configuration aspects (i.e. deploying the K8s dashboard) editing the file ```configuration.yaml```. Here an example of the default file. You must change it before the ```vagrant up``` command

```
---
dashboard: false
servers:
- name: k8s-master
  type: master
  box: ubuntu/xenial64
  box_version: 20180831.0.0
  eth1: 192.168.205.10
  mem: 2048
  cpu: 2
- name: k8s-worker-1
  type: node
  box: ubuntu/xenial64
  box_version: 20180831.0.0
  eth1: 192.168.205.11
  mem: 2048
  cpu: 2
- name: k8s-worker-2
  type: node
  box: ubuntu/xenial64
  box_version: 20180831.0.0
  eth1: 192.168.205.12
  mem: 2048
  cpu: 2
```

## How to check if the cluster is running?

You can access to each Virtual Machine (node in K8s terminology) with the command:

```
vagrat ssh <node name>
```

where <node name> could be ```k8s-master```, ```k8s-worker-1```, and ```k8s-worker-2```. To check if the cluster is running, and for all the administration tasks, you can access to the master node and use kubectl commands as vagrant user. The following command shows you the status of all the three nodes.
 
```
kubectl get nodes
```

You should see all the nodes in Ready status.

## Control the cluster from your laptop

Currently to manage the cluster you need to access your Vagrant machines via ssh to use kubectl commands. You can avoid this installing kubectl on your local machine and use it to control your cluster.

To do that you need to install kubectl on your machine [following this guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/). Then you need to copy the Kubernetes credentials from your remote host:

```
cd ~
scp -r vagrant@192.168.205.10:/home/vagrant/.kube .
```

Running the ```kubectl get nodes```command, you should see the cluster nodes.

**Note:** The second and next time you run this procedure you must:
1. remove the 192.168.205.10 ssh entry in ~/.ssh/known_hosts
2. remove the ~/.kube folder with the command ```rm -rf ~/.kube

## Clean-up

Execute the following command to remove the virtual machines created for the Kubernetes cluster.

```
vagrant destroy -f
```

You can destroy individual machines by vagrant destroy k8s-worker-1 -f

## Licensing

[Apache License, Version 2.0](http://opensource.org/licenses/Apache-2.0).
