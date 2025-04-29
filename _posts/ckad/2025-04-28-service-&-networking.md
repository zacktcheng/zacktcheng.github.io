# Service & Networking

### Table of contents:
- [Services](#services)
- [Ingress](#ingress)
- [Network Policies](#network-policies)

## Services

Kubernetes services are essential for enabling communication both within and outside of applications. They facilitate connections between various application
components and external users. In a typical setup, you might have pods dedicated to serving a front-end load to users, others running backend processes, and another
set connecting to external data sources. Services ensure connectivity among these pods, allowing front-end applications to be accessible to end users and enabling
smooth communication between back-end and front-end pods, as well as connectivity to external data sources, thereby promoting loose coupling between microservices
within an application.

To access a web application running on a Kubernetes pod from an external network, one must consider the networking setup. Typically, the Kubernetes node and external
user devices, such as laptops, have their own IP addresses, separate from the internal pod network. Direct access to the pod's IP is not possible from external
devices. Instead, a Kubernetes service acts as an intermediary, enabling the mapping of requests from a node to the pod running the web application. This type of
service, known as a NodePort service, listens on a port on the node and forwards requests to the pod's port.

NodePort services are one of several types available in Kubernetes. ClusterIP services create virtual IPs within the cluster to facilitate internal communication,
while LoadBalancer services provision load balancers to distribute traffic across multiple web servers in a front-end tier. This flexibility allows Kubernetes to
handle various networking scenarios efficiently.

Creating a NodePort service involves defining a service in a YAML file with the API version, kind, metadata, and spec sections. The spec section specifies the type
as NodePort and configures the ports involved, namely the target port on the pod, the port on the service, and the node port, which must fall within a predefined
range. A crucial part of the service definition is linking it to the correct pods using labels and selectors, ensuring that requests reach the appropriate
destination.

Once a service is defined and created using kubectl, it can be accessed via the designated node port, allowing users to reach the web application hosted by the pods.
In production environments with multiple pod instances for high availability, the same labels and selectors ensure the service routes requests to all relevant pods,
acting as a built-in load balancer using a random algorithm.

In scenarios where pods are distributed across multiple nodes, Kubernetes automatically extends the service to span all nodes, mapping the target port to the
designated node port on each node. This means the application can be accessed using the IP of any node in the cluster, providing flexibility and redundancy. Overall,
a Kubernetes service is highly adaptive: it automatically updates itself as pods are added or removed, freeing administrators from needing to make manual adjustments
post-creation.

### Cluster IP

A full-stack web application often comprises various pods, each hosting distinct components of the application. Typically, these include pods for the front-end web
server, backend server, a key-value store like Redis, and a persistent database such as MySQL. Establishing connectivity between these components is crucial.
The web front-end server, for instance, must communicate with backend servers, and those backend servers need to interface with both the database and Redis services.

Pods in a Kubernetes cluster are assigned IP addresses, but these addresses are not static due to the dynamic nature of pod lifecycle—pods can be terminated and new
ones created at any time. Relying on these non-static IP addresses is impractical for internal application communication. Moreover, a front-end pod might require
access to a backend service, raising the question of which backend pod to connect to and who manages these connections.

Kubernetes services address these connectivity challenges by grouping pods together and providing a single interface to access these grouped pods. For instance, a
service for backend pods can unify them under a single interface, ensuring requests are distributed randomly to one of the grouped pods. Similarly, creating a
service for Redis allows backend pods to access Redis systems seamlessly. This approach facilitates deploying a robust microservices-based application within a
Kubernetes cluster, allowing each component to scale or relocate without disrupting inter-service communication.

Each service within Kubernetes is assigned a ClusterIP and a name within the cluster, providing a stable point of access for other pods. These services are typically
of the ClusterIP type, which serves as the default service type in Kubernetes. Creating a ClusterIP service involves using a service definition file. The file
includes attributes like API version, kind, metadata, and spec. The API version is v1, the kind is service, and a name is assigned—such as 'backend.' The service
type is set to ClusterIP, enabling internal communication.

The service definition file outlines where the backend is exposed via ports, typically setting both the target and service ports to a common value, like 80. Linking
the service to specific pods utilizes selectors, which draw labels from pod definitions to ensure accurate connectivity. Once defined, a service is created using the
kubectl create command, and its status is checked with kubectl get services. Access to the service is facilitated through either the assigned ClusterIP or the
service's name within the cluster, ensuring consistent and reliable connectivity among the application's microservices.

## Ingress

Ingress in Kubernetes is a crucial component when deploying applications that require external access. Consider a scenario where you're deploying an online store
application on Kubernetes with the domain my-online-store.com. The application is containerized into a Docker image and deployed in a cluster. For database needs, a
MySQL service using ClusterIP is set up to enhance internal communication between the app and the database pods. To expose the application to users, a NodePort
service is created. This type of service publishes the app on a high port number like 38080, accessible through the node's IP address. As the demand for the
application grows, replicas are increased, and Kubernetes balances the traffic efficiently across them.

However, NodePort services require users to remember IP addresses and high port numbers, a suboptimal experience. To streamline access, you might configure a DNS
server to map the domain to the node's IP. But users still need to append the port number, a limitation since NodePorts assign only high ports above 30,000.
Introducing a proxy can map the standard port 80 to the NodePort, simplifying access to my-online-store.com.

When hosting on public cloud services like Google Cloud Platform, instead of a NodePort, a LoadBalancer service is optimal. Kubernetes not only provisions a high
port but also triggers the cloud provider to set up a network load balancer, which receives an external IP for direct access. DNS adjustments point to this IP,
enabling seamless access via my-online-store.com.

As your business expands, you offer new services like a video streaming service and need URL-based routing, e.g., my-online-store.com/watch for videos and /wear for
the store. Traditionally, provisioning separate load balancers for each service is expensive and cumbersome. Ingress offers a solution, functioning as a Layer 7 load
balancer that routes traffic based on URL paths within a single, cohesive framework. Ingress also supports SSL configurations centrally, relieving developers from
handling SSL in their code and simplifying cluster management as your application scales.

Setting up Ingress involves deploying an Ingress controller, such as nginx. An Ingress controller continually watches for Ingress resource changes and dynamically
manages the routing configuration for applications within the cluster. This centralized setup simplifies domain and path-based traffic management while maintaining
SSL settings.

The process of implementing Ingress involves creating an Ingress controller, usually as a Deployment, and configuring it via definition files similar to other
Kubernetes objects. The example here uses nginx as the controller, requiring a proper setup including environment variables and service accounts with appropriate
permissions. Once the Ingress controller is in place, Ingress resources are configured to specify routing rules according to domain and path requirements.

For routing traffic based on URL paths, a single HTTP Ingress rule is created with multiple path specifications, directing traffic to the appropriate backend
services based on the URL suffix. In the case of domain-based traffic splitting, multiple Ingress rules are defined, each tied to specific domain names or
hostnames.

In Ingress configurations, default backends handle traffic that doesn't match any specific rule, ensuring users receive appropriate feedback, such as a 404 error,
for undefined routes. This systematic use of Ingress in Kubernetes improves manageability of application access, facilitates microservice architectures, and
leverages internal and external configurations efficiently without external load balancer redundancy.

## Network Policies

Let's begin with a simple example of traffic flow within a web application consisting of a web server, an application server, and a database server. A user sends a
request to the web server at port 80. From there, the web server forwards the request to the backend API server communicating through port 5000. The API server then
retrieves data from the database server via port 3306 and relays the data back to the user. This illustrates two traffic types: ingress and egress. In this context,
the user's incoming request to the web server is ingress traffic, while the server's outgoing request to the API server is egress traffic, and vice versa for the
API and database servers.

To ensure a smooth traffic flow, specific rules must be established. For the web server, there needs to be an ingress rule to accept HTTP traffic on port 80 and an
egress rule for traffic on port 5000 to the API server. The API server also requires an ingress rule for port 5000 and an egress rule allowing traffic on port 3306
to the database. Similarly, the database server necessitates an ingress rule for port 3306. These foundational rules dictate the traffic flow between components in
the application, underpinning the network setup.

Shifting focus to Kubernetes network security, clusters typically consist of nodes hosting numerous pods and services, each assigned unique IPs. Kubernetes
networking solutions enable pods to communicate seamlessly within a virtual private network (VPN) that traverses all nodes. By default, Kubernetes is configured
with an all-allow rule, facilitating unrestricted communication between pods and services within the cluster.

Reflecting on our example, each application component is deployed as a separate pod—one for the web server, one for the API server, and one for the database.
Services oversee communication between these pods and external users. Default Kubernetes settings allow full pod interaction. However, to comply with security
requirements, such as prohibiting direct communication from the web server to the database, network policies can be implemented. These policies permit communication
to the database only from the API server, introducing essential security controls.

Network policies in Kubernetes act as objects, akin to pods or services, linked to pods via labels and selectors. These policies define ingress and egress rules,
specifying traffic directions. For instance, setting rules in the network policy allows ingress traffic only from the API pod to the database server on port 3306,
effectively blocking all other traffic. This configuration is critical to enforcing stringent network security protocols.

To apply a network policy to a pod, the same technique of using labels and selectors helps tie the policy to the appropriate pod. Under "Policy Types," you specify
whether to allow ingress, egress, or both, as required. In our case, setting an ingress policy restricts database access only to the API server. A definition file
for the network policy includes the API version, kind, metadata, and specifications tailored to meet specific traffic rules.

In practice, running the kubectl create command sets this policy in motion. It's crucial to note that network support varies among Kubernetes networking solutions;
popular ones like Kube Router, Calico, Romana, and Weave Net enforce network policies, whereas Flannel does not. Despite this, network policies can still be defined
but won't be operational in unsupported environments, highlighting the necessity for reviewing network solutions' documentation to confirm policy support.
