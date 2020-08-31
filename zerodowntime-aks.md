# How to achieve zero downtime of Kubernetes Services

Upgrading of Kubernetes Cluster onto a new version is no easy task, but doable!
The trick is to establish a workflow and stick to it. It looks like this:

1. Use a cluster containing of 2 node pools

We enhance our cluster with an addional nodepool, which will host the running
services, while we are upgradeing the first nodepool onto a higher version.

`$ az aks nodepool add --resource-group rg-aks-test --cluster-name k8scluster --node-vm-size Standard_DS2_v2 --name second --node-count 2` 


2. We are running now a cluster with 2 node pools, next step is to shift all running services onto the new cluster nodes

`$ kubectl get nodes -o wide` 

NAME | STATUS | ROLES | AGE | VERSION | INTERNAL-IP | EXTERNAL-IP | OS-IMAGE | KERNEL-VERSION | CONTAINER-RUNTIME
---- | ------ | ----- | --- | ------- | ----------- | ----------- | -------- | -------------- | -----------------
aks-default-89710360-vmss000000 | Ready | agent | 268d | v1.17.7 | 10.142.22.4 | <none> |  Ubuntu 16.04.6 LTS | 4.15.0-1089-azure | docker://3.0.10+azure
aks-default-89710360-vmss000001 | Ready | agent | 252d | v1.17.7 | 10.142.22.65 | <none> |  Ubuntu 16.04.6 LTS | 4.15.0-1089-azure | docker://3.0.10+azure
aks-second-89710360-vmss000000 | Ready | agent | 20m | v1.17.7 | 10.142.22.187 | <none> |  Ubuntu 16.04.6 LTS | 4.15.0-1089-azure | docker://3.0.10+azure
aks-second-89710360-vmss000001 | Ready | agent | 21m | v1.17.7 | 10.142.23.53 | <none> |  Ubuntu 16.04.6 LTS | 4.15.0-1089-azure | docker://3.0.10+azure

3. Now we can just disable the nodes on the first node pool, all pods get evicted and are restarted on the other node pool. Just take care that your 
replica count is greater than 1

`$ kubectl drain aks-default-89710360-vmss000000` 

`$ kubectl drain aks-default-89710360-vmss000001` 

4. Next step is upgrade the k8s control plane onto the new version.

`$ az aks get-upgrades --resource-group rg-aks-test  --name k8scluster` 

and the pick your version (i'm using 1.17.9 here as an example)

`$ az aks upgrade --resource-group rg-aks-test  --name k8scluster --kubernetes-version 1.17.9 --control-plane-only` 

5. After having upgraded the control plane, we can upgrade the first node pool

`$ az aks nodepool upgrade --resource-group rg-aks-test  --cluster-name k8scluster --kubernetes-version 1.17.9 --name default ` 

6. The next step is simple, just uncordon all nodes in the node pool *default* and drain each node in node pool *second*

7. Finally you can upgrade the node pool *second* or just delete it. But be aware that you need at least one node pool in the status of *System*

`$ az aks nodepool update --resource-group rg-aks-test --cluster-name k8scluster --mode System`

`$ az aks nodepool delete --resource-group rg-aks-test --cluster-name k8scluster --name second`

 
