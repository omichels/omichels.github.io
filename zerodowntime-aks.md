# How to achieve zero downtime of Kubernetes Services

Upgrading of Kubernetes Cluster onto a new version is no easy task, but doable!
The trick is to establish a workflow and stick to it. It looks like this:

1. Use a cluster containing of 2 node pools

We enhance our cluster with an addional nodepool, which will host the running
services, while we are upgrade the first nodepool onto a higher version.

`$ az aks nodepool add --resource-group rg-aks-test --cluster-name k8scluster --node-vm-size Standard_DS2_v2 --name second ` 


2. We are running now a cluster with 2 node pools, next step is to shift all running services onto the new cluster nodes

`$ kubectl get nodes -o wide` 

NAME                              STATUS   ROLES   AGE    VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-default-89710360-vmss000000   Ready    agent   268d   v1.17.7   10.142.22.4     <none>        Ubuntu 16.04.6 LTS   4.15.0-1089-azure   docker://3.0.10+azure
aks-default-89710360-vmss000001   Ready    agent   252d   v1.17.7   10.142.22.65    <none>        Ubuntu 16.04.6 LTS   4.15.0-1089-azure   docker://3.0.10+azure
aks-default-89710360-vmss000002   Ready    agent   223d   v1.17.7   10.142.22.126   <none>        Ubuntu 16.04.6 LTS   4.15.0-1089-azure   docker://3.0.10+azure
aks-second-89710360-vmss000003   Ready    agent   202d   v1.17.7   10.142.22.187   <none>        Ubuntu 16.04.6 LTS   4.15.0-1089-azure   docker://3.0.10+azure
aks-second-89710360-vmss000005   Ready    agent   67d    v1.17.7   10.142.22.248   <none>        Ubuntu 16.04.6 LTS   4.15.0-1089-azure   docker://3.0.10+azure
aks-second-89710360-vmss000006   Ready    agent   67d    v1.17.7   10.142.23.53    <none>        Ubuntu 16.04.6 LTS   4.15.0-1089-azure   docker://3.0.10+azure


`$ kubectl drain aks-default-89710360-vmss000000` 
`$ kubectl drain aks-default-89710360-vmss000001` 
`$ kubectl drain aks-default-89710360-vmss000002` 
 
