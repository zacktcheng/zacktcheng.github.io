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

<img src="/assets/images/deployment.drawio.png" width="auto" height="256" ><br>
Deployment in a nutshell.

When deploying an application in a production environment, it's essential to consider several factors to ensure stability and efficiency. For a web server, multiple instances should be running to handle user load effectively. Additionally, seamless upgrades of Docker instances are crucial when new application versions are available in the Docker registry. To prevent user disruptions, implementing rolling updates rather than upgrading all instances simultaneously is advisable. This approach ensures that upgrades occur progressively, reducing potential impacts on users. In cases where an upgrade leads to errors, the ability to roll back changes is vital to maintain system reliability.

Moreover, making various changes, such as updating web server versions, scaling the environment, and modifying resource allocations, simultaneously is often necessary. In such situations, it's beneficial to pause the environment, apply all changes, and then resume operations to roll out updates collectively. These functionalities are achievable with Kubernetes deployments, which offer advanced features compared to basic pods.

Up to this point, discussions on Kubernetes have focused on pods, which deploy single application instances in containers. Replication controllers or replica sets handle the deployment of multiple pods. However, deployments, a higher-level Kubernetes object, provide enhanced capabilities, including seamless upgrades through rolling updates, the ability to undo changes, and pausing and resuming updates.

To create a deployment, start by defining a deployment definition file, similar to a replica set definition but identified as a deployment. The file includes specifications such as API version, metadata, and a pod definition template. After preparing the file, use the Kube control create command to initiate the deployment. The deployment automatically generates a replica set, which, in turn, creates pods.

While there are similarities between replica sets and deployments, deployments introduce a new Kubernetes object with distinct benefits. Upcoming lectures will explore practical applications of deployments. Finally, use the Kube control get all command to view all created objects, revealing the hierarchy of the deployment, replica set, and pods, effectively illustrating the deployment's capabilities within a Kubernetes environment.

## Namespaces

To understand namespaces in Kubernetes, consider an analogy where two boys named Mark are identified by their last names, Smith and Williams, to distinguish them from each other. They belong to different households, each with its own members, resources, and rules. Members within each house use first names to address each other, while outsiders use full names. Similarly, in Kubernetes, namespaces act as isolated sections within a cluster. Previously, Kubernetes objects like Pods, Deployments, and Services have been created in the default namespace, automatically set up by Kubernetes.

The default namespace is suitable for smaller environments or learning scenarios, but enterprise or production environments benefit significantly from user-defined namespaces. Kubernetes also creates additional namespaces, such as kube-system for internal resources, and kube-public for resources accessible to all users. User-created namespaces allow for resource isolation, as seen when differentiating between development and production environments. Each namespace can have distinct policies and resource quotas, ensuring fair usage and avoiding excess consumption.

Inters within a namespace can communicate using simple names, like 'DB service' for a database service. To communicate between namespaces, appending the namespace to the service’s name is required, using a format like service-name.namespace.svc.cluster.local (e.g., dbservice.dev.svc.cluster.local). The DNS name of a service in Kubernetes follows this structure, aiding communication across namespaces. To manage resources effectively, Kubernetes provides kubectl commands to interact with namespaces.

Creating resources in different namespaces necessitates the namespace option in kubectl commands. Defining a namespace in resource definition files ensures automatic allocation upon creation. New namespaces can be established with namespace definition files or direct commands like kubectl create namespace followed by the namespace's name. Switching between namespaces is facilitated by the kubectl config command to set the active namespace for the current context, streamlining environment-specific operations.

To manage limited resources, Kubernetes supports resource quotas within namespaces. Defining such quotas involves specifying limits on resources like pods, CPUs, and memory in a resource quota definition file. By detailing these constraints under the spec section, administrators can ensure balanced resource distribution, preventing any namespace from monopolizing resources. Context, which involves managing multiple clusters and environments, will be explored further in subsequent discussions.
