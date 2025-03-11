# Container Orchestration

### Table of contents:
- [What is a Container](#what-is-a-container)
- [What is Container Orchestration](#So-what-is-container-orchestration)

## What is a Container

### Introducing Containers

A container is a form of OS virtualization that packages an application with all its necessary dependencies (code, libraries, runtime environment) allowing it to run consistently across different computing environments, essentially acting like a portable, lightweight virtualized application that can be easily deployed and managed on various platforms.

<img src="/assets/images/containerization.drawio.png" />

*Containers are a form of operating system virtualization.*

Containers are best suited to deliver microservices by providing portable, isolated virtual environments for applications to run without interference from other running applications. A container encapsulates microservices with their dependencies inside; However, containers do not run them directly, they run container images instead.

<img src="/assets/images/what-is-containerization.png" width="512px" />

*Virtualization with Hyper-v versus a container engine.*

### What about the Container Images?

Container image is a lightweight, standalone, executable software package. It includes everything needed to run an application: code, runtime, system tools, system libraries and settings, and it represents the source of a container deployed to offer an isolated executable environment for the application. Containers can be deployed from a specific image on many platforms, such as workstations, Virtual Machines, public cloud, etc.

<img src="/assets/images/containerImage.drawio.png" />

*How does a container image work: from Dockerfile to image to container.*

## So, What is Container Orchestration?

Container orchestration is a process to automatically porvisions, deploys, scales and manages containerized applications without worrying about the underlying infrastructure. Once the containers are running, container orchestration tools automate life cycle management and operational tasks based on the container definition file, including:
- Provisioning and deployment
- Scaling containers up or down and load balancing
- Allocating resources between containers
- Moving containers to another host to ensure availability if there’s a shortage of resources or an unexpected outage
- Performance and health monitoring of the application
- Allocating resources between containers
- Service discovery

### The benifits of Using Container Orchestrators

- Group hosts together while creating a cluster, in order to leverage the benefits of distributed systems.
- Schedule containers to run on hosts in the cluster based on resources availability.
- Enable containers in a cluster to communicate with each other regardless of the host they are deployed to in the cluster.
- Bind containers and storage resources.
- Group sets of similar containers and bind them to load-balancing constructs to simplify access to containerized applications by creating an interface, a level of abstraction between the containers and the client.
- Manage and optimize resource usage.
- Allow for implementation of policies to secure access to applications running inside containers.

<img src="https://www.pegasusone.com/wp-content/uploads/2019/11/container_orchestration_tools.jpg" />

*Popular container orchestration tools.*
