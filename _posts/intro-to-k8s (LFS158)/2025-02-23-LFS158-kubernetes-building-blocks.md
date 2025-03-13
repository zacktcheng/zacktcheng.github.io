# Kubernetes Building Block

### Table of contents:
- [Nodes, Namespaces, and Pods](#nodes-namespaces-and-pods)
- [Labels and Label Selectors](#labels-and-label-selectors)
- [ReplicaSets, DaemonSets, and Deployments](#replicasets-daemonsets-and-deployments)
- [Services](#services)

## Nodes, Namespaces, and Pods

### Nodes

A node may be a virtual or physical machine, depending on the cluster. Each node is managed by the control plane and contains the services necessary to run Pods. Each node is managed with the help of two Kubernetes node agents - kubelet and kube-proxy.

Based on their predetermined functions, there are two distinct types of nodes - control plane and worker. A typical Kubernetes cluster includes at least one control plane node, but it may include multiple control plane nodes for the High Availability (HA) of the control plane. In addition, the cluster includes one or more worker nodes to provide resource redundancy in the cluster.

The control plane nodes run the control plane agents, such as the API Server, Scheduler, Controller Managers, and etcd in addition to the kubelet and kube-proxy node agents, the container runtime, and add-ons for container networking, monitoring, logging, DNS, etc.

Worker nodes run the kubelet and kube-proxy node agents, the container runtime, and add-ons for container networking, monitoring, logging, DNS, etc.

### Namespaces

In computing, a namespace is a scope that organizes identifiers (like variables, functions, classes, etc.) to prevent naming conflicts and improve code organization. If multiple users and teams use the same Kubernetes cluster we can partition the cluster into virtual sub-clusters using Namespaces. The names of the resources/objects created inside a Namespace are unique, but not across Namespaces in the cluster.

### Pods

A Pod is the smallest Kubernetes workload object. It is the unit of deployment in Kubernetes, which represents a single instance of the application. A Pod is a logical collection of one or more containers, enclosing and isolating them to ensure that they:
- Are scheduled together on the same host with the Pod
- Share the same network namespace, meaning that they share a single IP address originally assigned to the Pod
- Have access to mount the same external storage (volumes) and other common dependencies


## Labels and Label Selectors

### Labels

Labels are key-value pairs attached to Kubernetes objects such as Pods, ReplicaSets, Nodes, Namespaces and Persistent Volumes. Labels are used to organize and select a subset of objects, based on the requirements in place. Many objects can have the same Label(s). Labels do not provide uniqueness to objects. Controllers use Labels to logically group together decoupled objects, rather than using objects' names or IDs.

### Label Selectors



## ReplicaSets, DaemonSets, and Deployments

### ReplicaSets

A **replicationController** is an operator that ensures a specified number of replicas of a Pod are running at any given time, by constantly comparing the actual state with the desired state of the managed application. Extend from stated above, one of the most important feature that replicaController provides is **self-healing** for a pod. However, the default recommended controller is the **Deployment** which configures a ReplicaSet controller to manage application Pods' lifecycle.

A **ReplicaSet** is, in part, the next-generation ReplicationController, as it implements the replication and self-healing aspects of the ReplicationController. The ReplicaSet allows Kubernetes to run multiple instances of the application simultaneously, avoid one crash potentially adversely impacts other applications, services, or clients, hence achieving high availability. The replicaSet controlls the lifecycle of a pod, it allows us scale the number of replicas manually or through the use of an autoscaler.

ReplicaSets can be used independently as Pod controllers but they only offer a limited set of features. A set of complementary features are provided by Deployments, the recommended controllers for the orchestration of Pods.

### Deployments

The **DeploymentController** *AKA Deployment in short* is part of the control plane node's controller manager, and as a controller it also ensures that the current state always matches the desired state of our running containerized application.

The Deployment manages the creation, deletion, and updates of Pods. It allows for seamless application updates and rollbacks, known as the default RollingUpdate strategy, through rollouts and rollbacks, and it directly manages its ReplicaSets for application scaling.

#### RollingUpdate strategy

What triggers a rolling update (thus changes the revision number):
- Updating the container image
- Updating a container port, volumes, and mounts

What does not trigger a rolling update:
- Scaling the deployment
- Labeling the deployment

Rollouts

Rollbacks

### DaemonSets

A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. DaemonSet operators are commonly used in cases when we need to collect monitoring data from all Nodes, or to run storage, networking, or proxy daemons on all Nodes, to ensure that we have a specific type of Pod running on all Nodes at all times.

## Services

By default, the container does not expose its ports to the cluster's network, and it is not discoverable either; However, a containerized application deployed to a Kubernetes cluster may need to reach other such applications, or it may need to be accessible to other applications and possibly clients.

In Kubernetes, a service is a sophisticated port-mapping involved with the kube-proxy node agent, IP tables, routing rules, cluster DNS server, all collectively implementing a micro-load balancing mechanism that exposes a container's port to the cluster's network, even to the outside world if desire.
