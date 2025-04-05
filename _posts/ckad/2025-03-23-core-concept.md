# Core Concepts

### Table of contents:
- [Kubernetes Architecture](#kuberenetes-architecture)
- [Pods](#pods)
- [ReplicaSets](#replicasets)
- [Deployments](#deployments)
- [Namespaces](#namespaces)

## Kubernetes Architecture

### Docker vs ContainerD

## Pods

Prerequisities to work with pods on Kuberenetes:
1. The application's already been developed and pushed onto the Docker repository
2. K8s cluster is running properly

### The smallest key Kuberebetes component for CICD
Our ultimate goal is to delopy our applications in the form of containers on a set of work nodes; however, K8s does not deploy containers to the worker nodes explicitly. The containers are encapsulated into a k8s object known as pods. A pod is a single instance of an application.

When there's a need to scale up the application, we create a new pod with a ndew instance of the same application in this case. That is, the pod and an application usually have a one-to-one relationship. 

### Multi-container in One Pod (one-to-many)
One practicle exception is when the application requires a helper application such us processing a user data or uploading files, in this situation, the helper container lives alongside the application container within the same pod.
|      | Multi-container in one pod                          |
|------|-----------------------------------------------------|
| Pros | Sharing the same network, same storage space        |
| Cons | When one is paralyzed, all containers are paralyzed |

### How to deploy pods:
1. `kubectl run nginx --image nginx` Kuberenetes creates a new pod to run an nginx container pulled from a docker repositiory.
2. `kubectl get pods` Kuberenetes gets the pods running under the current namespace.

## ReplicaSets

# Namespaces
