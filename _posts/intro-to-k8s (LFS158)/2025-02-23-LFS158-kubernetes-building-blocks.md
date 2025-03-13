# Kubernetes Building Block

### Table of contents:
- [Nodes, Namespaces, and Pods](#nodes-namespaces-and-pods)
- [Labels and Label Selectors](#labels-and-label-selectors)
- [ReplicaSets, DaemonSets, and Deployments](#replicasets-daemonsets-and-deployments)
- [Services](#services)

## Nodes, Namespaces, and Pods

### Nodes


### Namespaces


### Pods



## Labels and Label Selectors

### Labels


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



## Services

By default, the container does not expose its ports to the cluster's network, and it is not discoverable either; However, a containerized application deployed to a Kubernetes cluster may need to reach other such applications, or it may need to be accessible to other applications and possibly clients.

In Kubernetes, a service is a sophisticated port-mapping involved with the kube-proxy node agent, IP tables, routing rules, cluster DNS server, all collectively implementing a micro-load balancing mechanism that exposes a container's port to the cluster's network, even to the outside world if desire.
