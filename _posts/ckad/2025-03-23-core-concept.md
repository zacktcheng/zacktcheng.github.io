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

### Creating a pod with YAML
All Kubernetes definition file always contain four top-level fields:
1. API version: either `v1` or `apps/v1`, depending on the kind
2. Kind: type of objects we want to create, e.g., pod, service, .ect
3. Metadata:
   - metadata of the object, namely `name` and `label`
   - can have any key-value pairs under `label`
4. Spec:
   - `containers` is an array because a pod can have multiple containers
   - A dash before a key means that this is the first time in the array

## ReplicaSets

Why is the replicaSets important:
1. The replication controller assure no downtime for the users to access the application
2. Replication helps load balancing and scaling because the replication can span accross multiple nodes in a cluster when one node is out of the memory space

Replication vs Replica Set:
- Replication controller is the old technology while the replica set is the new recommended way of making replication
- Stick to the replica set

ReplicaSet yaml example:
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myap
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      label:
        app: myapp
        type: front-end
  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

## Deployments

## Namespaces
