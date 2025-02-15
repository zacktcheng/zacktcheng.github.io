# Kubernetes Architecture

### Table of contents:
- [Control Plane](#control-plane)
- [Worker Nodes](#worker-nodes)
- [Networking](#networking)

## Control Plane

The Kubernetes control plane is a **collection of processes** that manages the state of a Kubernetes cluster.

### Control Plane Node Components
- API Server
  - The only control plane component to talk to the key-value store.
- Scheduler
- Controller Manager
- Key-value Data Store

## Worker Nodes

A worker node provides a running environment for client applications. These applications are microservices running as application containers. The application containers are encapsulated in Pods, controlled by the cluster control plane agents. A Pod is the smallest scheduling work unit in Kubernetes.

### Worker Node Components
- Container Runtime
- Kubelet
- Kube-Proxy
- Add-ons

## Networking

In a multi-worker Kubernetes cluster, the network traffic between client users and the containerized applications deployed in Pods is handled directly by the worker nodes

### Networking Challenges
- Container-to-Container communication inside Pods
- Pod-to-Pod communication on the same node and across cluster nodes
- Service-to-Pod communication within the same namespace and across cluster namespaces
- External-to-Service communication for clients to access applications in a cluster
