---
layout: post
current: post
cover: assets/images/cover-img/c22.jpg
navigation: True
title: What is Kubernetes
date: 2020-06-15
comments: true
---

**In [previous post](./docker-and-container), I introduced a novel technology, Docker. However, there are some issues using them. In this post, I will introduce another technology that tackles one major issue of Docker. This technology is Kubernetes. I won't cover how to use Kubernetes in details; instead I will introduce basic concepts and why do we want to use it**

---

**What's wrong with Docker?**

Recall the biggest benefit of Docker is that we isolate an application in a container and people can run it independent of their current enviornment in the local machines because all dependencies needed are packed together as a whole into a docker image.

However, docker focuses on low level implementation like how to start, update or delete a container. In many cases when you have many containers to work collaboratively, what if you want to scale your containers? Of course, it's very tedious and error-prone to mannually orchestrate them. Kubernetes provides the exact high level orchestrtion you need specifically for your containerized application such as how many nodes/pods to run, which network to assign and to which pod, what to do if one pod fails, etc.

**Again, before dive right in, let's have a big picture of Kubernetes and understand some jargons**

Fisrt, we call a deployed kubernetes service a cluster. In each cluster, we have two major components, **masters** and **nodes**.
<img src="assets/images/Kubernetes/K8-1.jpg" alt="K8-1" style="width: 80%;">

- **Master**: the "control panel", of which the apiserver is the most important component.

  1.  **Scheduler**
      - assign works to node
      - watch apiserver for new pods
  2.  **Cluster Storage**
      - persistent storage of the cluster state and config
  3.  **Controller**
      - controlls everything, including node, namespace, endpoint, etc.
      - watch for any changes in the cluster
  4.  **apiserver**
      - an exposed api that is used as kubectl in cli - consumes JSON or YAML file via manifest - sometimes used interchangeably with master
        <img src="assets/images/Kubernetes/K8-2.jpg" alt="K8-2" style="width: 80%;">

- **Node**: a working machine

  1. **Kubelet**

     - communicates with master to receive assignments and provide feedback to master such as the failure of a pod
     - instantiates pods once assignments received
     - monitors the status of pods

  2. **Container Runtime**

     - we need this to actually run our containers, i.e pull container image and make it to the running container and run/stop it

  3. **Kube-proxy**

     - a network proxy that runs on each node in a cluster
     - assign IP address to each pod. In other words, all containers in a pod share the same IP address
     - it also does load balancing
       <img src="assets/images/Kubernetes/K8-3.jpg" alt="K8-3" style="width: 50%;">

- **Pod**: the basic unit to run the containerized application.

  - usually has one container but are legit to have more
  - Kubernets does not control container directly. Instead, it controlls pods.
  - a network proxy that runs on each node in a cluster
  - usually has five states in its lifecycle: _Pending, Running, Succeeded, Failed, and Unknown_

- **Service**: expose an application running on a set of Pods as a network service

  - One of its jobs is load balancing, but only send to healthy pods
  - Pods tie with services by label(s), defined in metadata in pod yaml file
  - It can point to things outside of the cluster
  - Use TCP by default
  - Ingress help services do all these

- **Namespace**

  So by now, you should already know that each running Kubernetes service is a cluster. One of the powerful things Kubernetes supports is you can have multiple virtual clusters within a physical cluster. These virtual clusters are called namespaces. For most of the time when you have a few to tens of the users using the cluster, you don't even need to think about namespaces. Namespace is rather useful when you have many users spread across multiple teams, or projects and cluster resources are divided to each namespace via resource quota.

**How to use Kubernetes?**

Nowadays alomost all cloud platforms provide Kubernetes as a Service. You can also spin up your Kubernetes cluster easily on your local using the tool `Minikube`, which runs a single node Kubernetes cluster inside a VM on your laptop.

- To learn how to use `Minikube`, go to <a href="https://minikube.sigs.k8s.io/docs/start/" style="font-weight: normal;">Minikube Documentation </a>. You need to download **minikube** and **kubectl**.

- To learn how to use Kubernetes up in the cloud, refer to any cloud providers you'd like. I personally used Azure Kubernetes Services, so if you happen to be the same, go to <a href="https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-cluster" style="font-weight: normal;">this tutorial </a>
