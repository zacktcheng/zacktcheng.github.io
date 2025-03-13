# Kubernetes

### Table of contents:
- [From Borg to Kubernetes](#from-borg-to-kubernetes)
- [What is Kubernetes](#what-is-kubernetes)
- [Kubernetes Features](#kubernetes-features)

## From Borg to Kubernetes

According to the abstract of Google's Borg paper:

*"Google's Borg system is a cluster manager that runs hundreds of thousands of jobs, from many thousands of different applications, across a number of clusters each with up to tens of thousands of machines."*

For more than a decade, Borg has been Google's secret, running its worldwide containerized workloads in production. Services we use from Google, such as Gmail, Drive, Maps, Docs, etc., are all serviced using Borg.

Among the initial authors of Kubernetes were Google employees who have used Borg and developed it in the past. They poured in their valuable knowledge and experience while designing Kubernetes. Several features/objects of Kubernetes that can be traced back to Borg, or to lessons learned from it, are:
- API servers
- Pods
- IP-per-Pod
- Services
- Labels. 

## What is Kubernetes

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

Kubernetes comes from the Greek word κυβερνήτης, which means helmsman or ship pilot. With this analogy in mind, we can think of Kubernetes as the pilot on a ship of containers. Kubernetes is also referred to as k8s (pronounced Kate's), as there are 8 characters between k and s.

Kubernetes is highly inspired by the Google Borg system, a container and workload orchestrator for its global operations, Google has been using for more than a decade. It is an open source project written in the Go language. Started by Google and, with its v1.0 release in July 2015, Google donated it to the Cloud Native Computing Foundation (CNCF), one of the largest sub-foundations of the Linux Foundation.

## Kubernetes Features

Kubernetes offers a very rich set of features for container orchestration. Some of its fully supported features are:
- **Automatic bin packing**: Kubernetes automatically schedules containers based on resource needs and constraints, to maximize utilization without sacrificing availability.
- **Designed for extensibility**: A Kubernetes cluster can be extended with new custom features without modifying the upstream source code.
- **Self-healing**: Kubernetes automatically replaces and reschedules containers from failed nodes. It terminates and then restarts containers that become unresponsive to health checks, based on existing rules/policy. It also prevents traffic from being routed to unresponsive containers.
- **Horizontal scaling**: Kubernetes scales applications manually or automatically based on CPU or custom metrics utilization.
- **Service discovery and load balancing**: Containers receive IP addresses from Kubernetes, while it assigns a single Domain Name System (DNS) name to a set of containers to aid in load-balancing requests across the containers of the set.

Additional fully supported Kubernetes features are:
- **Automated rollouts and rollbacks**: Kubernetes seamlessly rolls out and rolls back application updates and configuration changes, constantly monitoring the application's health to prevent any downtime.
- **Secret and configuration management**: Kubernetes manages sensitive data and configuration details for an application separately from the container image, in order to avoid a rebuild of the respective image. Secrets consist of sensitive/confidential information passed to the application without revealing the sensitive content to the stack configuration, like on GitHub.
- **Storage orchestration**: Kubernetes automatically mounts software-defined storage (SDS) solutions to containers from local storage, external cloud providers, distributed storage, or network storage systems.
- **Batch execution**: Kubernetes supports batch execution, long-running jobs, and replaces failed containers.
- **IPv4/IPv6 dual-stack**: Kubernetes supports both IPv4 and IPv6 addresses.
