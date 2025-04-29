# Multi-Container Pods

### Table of contents:
- [Multi-Container Pod Design Patterns](#multi-container-pod-design-patterns)
- [Init Containers](#init-containers)

## Multi-Container Pod Design Patterns

In certain cases, such as when a web server needs a logging service, two services might require close collaboration without merging their codes. Multi-container pods are used, allowing services to share the same lifecycle, network space, and storage volumes, thus simplifying communication between them. The pod definition file can include multiple containers, like a "log agent," by expanding the container array in the spec section.

1. Sidecar Pattern: Involves deploying a logging agent alongside a web server to manage and forward logs to a central log server.
   - Drawbacks: When there're multiple applications generating logs in different formats, it would be hard to process the various formats.
3. Adapter Pattern: Improved from the sidecar pattern which utilizes an adapter container to convert logs into a common format before sending them to a central server.
   - Drawbacks: We must make sure to modify this connectivity in the application code to accommodate with the environment to deploy.
5. Ambassador Pattern: Improved from the adapter pattern by creating an abstraction layer for a database connection, allowing an application to always refer to a database at localhost while the ambassador container redirects the request to the appropriate database.

## Init Containers

In a multi-container pod, each container is expected to run a process that stays alive as long as the POD's lifecycle. For example in the multi-container pod that we talked about earlier that has a web application and logging agent, both the containers are expected to stay alive at all times. The process running in the log agent container is expected to stay alive as long as the web application is running. If any of them fails, the POD restarts.

But at times you may want to run a process that runs to completion in a container. For example a process that pulls a code or binary from a repository that will be used by the main web application. That is a task that will be run only one time when the pod is first created. Or a process that waits for an external service or database to be up before the actual application starts. That's where initContainers comes in.

An initContainer is configured in a pod like all other containers, except that it is specified inside a `initContainers` section, like this:
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ;']
```
When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts.

You can configure multiple such initContainers as well, like how we did for multi-pod containers. In that case each init container is run **one at a time in sequential order**.

If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```
