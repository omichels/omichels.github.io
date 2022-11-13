---
layout: post
title: Kubernetes Virtual Nodes
updated: 2022-11-13
category: posts
---


Just a quick reminder that Kubernetes is a total gamechanger for all software development projects.
Providings new environments or stages does no longer take weeks or days any longer but can be managed 
with minutes.

Deploying a typical Azure AKS Cluster can be accomplished in around 30 minutes,
but the concept of "virtual nodes" gives a lot of more agility.

A typical AKS Kubernetes Cluster consists of dedicated nodes. These 
nodes are always on and must be paid for. But what if you need to scale up or down and don't want to waste money.
Sure adding addional nodes is easy, just scale out your cluster. But this may take some time.

The solution may be to switch to "virtual nodes". This is accomplished through the 
relative new open source project [virtual-kubelet](https://github.com/virtual-kubelet/virtual-kubelet),
enabling to add kubelets (the node agent running on the kubernetes node) in a pluggable way.
This means that the node agent and therefore the related VM can be plugged-in or -off in essential no-time.

So, using spot images from the Cloud may be the next logical step to save costs during development phases
with fluctuating ressource needs.
 
