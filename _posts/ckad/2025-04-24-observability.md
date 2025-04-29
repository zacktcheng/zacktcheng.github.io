# Obvservability

### Table of contents:
- [Readiness and Liveness Probes](#readiness-and-liveness-probes)
- [Liveness Probes](#liveness-probes)
- [Container Logging](#container-logging)
- [Monitor and Debug Applications](#monitor-and-debug-applications)

## Readiness and Liveness Probes

*Recap:*

*A pod has a pod status, and some conditions. The pod status tells us where the pod is in its lifecycle. When a pod is first created, it is in a pending state.
This is when the scheduler tries to figure out where to place the pod. If the scheduler cannot find the node to place the pod, it remains in a pending state.*

*Once the pod is scheduled, it goes into a container creating status where the images required for the application are pulled and the container starts. Once all
the containers in a pod starts, it goes into a running state where it continues to be until the program completes successfully or is terminated.*

Pod conditions in Kubernetes act as indicators of a pod's state, using an array of true or false values. Each condition reflects different stages, such as when a
pod is scheduled on a node or initialized. Importantly, once all containers within a pod are ready, the pod itself is deemed ready, signifying that the application
is operational and can handle user traffic. These statuses can be examined with commands like kubectl describe pod and kubectl get pods, focusing on the conditions
section to verify readiness.

However, readiness status may not always accurately reflect an application’s true state, as various applications require different amounts of time to initialize.
For example, while a simple script might be ready in milliseconds, a database service may take seconds, and a large web server can take minutes. This discrepancy
can lead Kubernetes to prematurely mark a pod as ready, thus allowing traffic to be routed to an application that isn't fully operational.

To address this, developers can configure readiness probes in the pod definition file, aligning the pod's "ready" condition with the actual state of the application.
Probes can be set up to perform different tests, such as HTTP requests to server APIs, checking TCP sockets for databases, or executing custom scripts within the
container. These probes ensure that an application is genuinely ready before traffic is directed to it. Kubernetes only sets the ready condition to true once these
probes are satisfied, delaying traffic until the application is fully prepared.
This approach proves particularly vital in multi-pod setups, like replica sets or deployments. When adding a new pod, readiness probes prevent routing traffic to it 
until it's fully ready, thus avoiding service disruptions. Properly configured readiness probes mean traffic continues to flow smoothly to existing pods, ensuring
users aren't affected during new pod initialization. These strategies enhance the reliability and stability of services deployed in Kubernetes environments,
safeguarding the user experience.

## Liveness Probes

When you run an image of NGINX using Docker, it initially begins serving users efficiently. However, if the web server experiences a crash, leading to the exit of
the NGINX process, the container itself also exits. This change in the container's status is evident when you check using the docker ps command. Since Docker lacks
orchestration capabilities, it does not automatically attempt to revive the container. As a result, the container remains inactive, denying service to its users
until someone manually intervenes to create a new container.

This is where Kubernetes orchestration steps in. By running the same web application in Kubernetes, the platform intelligently manages service continuity. Each time
the application crashes, Kubernetes attempts to restart the container automatically, thus restoring service to users. This automated process is visible through the
increasing restart count in the output of the kubectl get pods command, demonstrating Kubernetes' capability in maintaining application uptime.

However, a challenge arises when the application within a container might not work properly despite the container remaining operational. For example, a code bug
could cause the application to enter an infinite loop. In this scenario, Kubernetes perceives the container as active and assumes normal application function,
yet users interacting with the container are left unserviced. To remedy such situations, restarting or destroying the container and deploying a new one becomes
necessary.

Liveness probes in Kubernetes address these concerns by periodically testing the actual health of an application within a container. If these tests, which might
involve checking the operability of an API server in a web application or verifying an active TCP socket for databases, fail, the container is marked unhealthy.
Consequently, it is destroyed and recreated. Developers have the flexibility to define what constitutes a healthy application, tailoring liveness probes to
specific application requirements.

Liveness probes are configured in the pod definition file, similar to readiness probes, but specifically under the liveness category. They include options such as
HTTP GET for APIs, TCP sockets for ports, and custom commands for executing specific tests. Additionally, developers can set initial delays before tests commence,
define the frequency of tests with the periodSeconds option, and establish success and failure thresholds, providing a comprehensive strategy to ensure application
integrity and availability.

## Container Logging

Logging in Docker involves capturing the output generated by applications running within a Docker container. In a scenario where a Docker container named "event
simulator" is initiated to generate random events, simulating a web server environment. These events are streamed to the standard output. When this Docker container
is run in detached mode using the "-d" option, the logs are not visible immediately. To view these logs, the docker logs command can be employed, followed by the
container ID. The `-f` option allows users to follow the live log trail as events are generated.

Transitioning to Kubernetes, logging operates similarly but within a more complex orchestration environment. When a pod is created using a pod definition file with
the same Docker image, logs can be accessed through the kubectl logs command using the pod's name. The "-f" option facilitates real-time log streaming, mirroring
the functionality available in Docker. However, it is important to recognize that the logs correspond to the container running inside the pod.

Kubernetes pods can host multiple Docker containers, which necessitates additional considerations for logging. If a pod definition file is updated to include another
container, such as "image processor," specifying the container name becomes critical when fetching logs. Using kubectl logs with only the pod name, without
indicating which container's logs are needed, could result in a command failure, prompting the user to specify a container name. In such cases, identifying the
desired container, like "event simulator," is essential to retrieve the correct log messages, ensuring clarity and precision in log management within multi-container
Kubernetes pods.

## Monitor and Debug Applications

Monitoring resource consumption in Kubernetes involves tracking both node-level and pod-level metrics. At the node level, it's important to assess the number of nodes
in a cluster, their health status, and performance metrics such as CPU, memory, network, and disk utilization. Similarly, at the pod level, monitoring involves
examining the number of pods and evaluating each pod's CPU and memory consumption. To achieve this, a solution is required to monitor, store, and analyze these
metrics effectively.

Kubernetes does not inherently include a comprehensive monitoring solution, prompting users to rely on various open-source and proprietary options. Among open-source
solutions are Metrics Server, Prometheus, and The Elastic Stack, while proprietary alternatives include Datadog and Dynatrace. For foundational knowledge,
particularly in the Kubernetes For Developers course, the focus is on the Metrics Server, while other solutions are explored in more advanced courses. Initially,
Hipster was used for monitoring and analysis in Kubernetes, but it has been deprecated in favor of Metrics Server, a streamlined in-memory monitoring tool.

The Metrics Server operates by collecting and aggregating metrics from Kubernetes nodes and pods. However, it stores these metrics only in memory, meaning that
historical performance data is unavailable through this tool alone. For historical data, users must turn to more advanced monitoring systems. Metrics are generated
within Kubernetes nodes by Kubelet, an agent responsible for running instructions from the Kubernetes API master server. Kubelet features a subcomponent known as
cAdvisor, which retrieves performance metrics from pods and shares them with the Metrics Server via the Kubelet API.

To employ the Metrics Server in a local Minikube cluster, users can enable it with the minikube addons enable metrics-server command. For other environments, the Metrics Server can be deployed by cloning its deployment files from a GitHub repository and using the kubectl create command to set up necessary components. This setup includes deploying pods, services, and roles that facilitate performance metric polling from cluster nodes. After deployment, allowing time for data collection and processing is essential. Once the data is ready, users can view cluster performance metrics through the kubectl top node command, which displays CPU and memory consumption for each node.
