# Configuration

### Table of contents:
- [Enviornment Variables](#environment-variables)
- [ConfigMaps](#configmaps)
- [Secrets](#secrets)
- [Serivce Account](#service-account)

## Environment Variables

To set an environment variable in Kubernetes, you use the env property. This property functions as an array, so each item in this array is denoted by a dash, marking it as an individual element within the array. Each item consists of two primary components: a name and a value property. The name represents the environment variable's identifier within the container, while the value defines the actual value assigned to this variable. This method of defining environment variables is straightforward, utilizing a simple key-value pair format for clarity and ease of use within the container environment.

## ConfigMaps

Managing numerous pod definition files can become challenging, particularly when dealing with environment data stored within them. To simplify this process, Kubernetes offers a solution through ConfigMaps, which allow the central management of configuration data in key-value pair format. When a pod is created, ConfigMaps can be injected into it, making these key-value pairs accessible as environment variables for applications running inside the container. Configuring ConfigMaps involves two primary steps: creating the ConfigMap and subsequently injecting it into the pod.

ConfigMaps, like other Kubernetes objects, can be created imperatively or declaratively. The imperative approach involves using the kubectl create configmap command along with the necessary arguments directly on the command line. This method enables users to specify key-value pairs using the `--from-literal` option, although handling too many configuration items in this manner can become cumbersome. Alternatively, configuration data can be input via a file using the `--from-file` option, which reads data from the specified path and stores it under the filename.

The declarative approach requires the creation of a ConfigMap definition file, similar to pod definition files. This file contains the API version, kind, metadata, and a data section with key-value pairs defining the configuration. Running the kubectl create command with this file establishes the ConfigMap, which can then be used across various applications. It's essential to name ConfigMaps appropriately for ease of use when associating them with pods. Viewing ConfigMaps is possible through the kubectl get configmaps and kubectl describe configmaps commands.

Once the ConfigMap is created, the next step is configuring it with a pod. This is achieved by adding an envFrom property in the container of the pod definition file, enabling the injection of ConfigMap data as environment variables. Each item listed in envFrom corresponds to a ConfigMap, allowing for flexibility in injecting configuration settings. This method allows the seamless integration of environment variables into applications; however, additional methods for injecting configuration data exist, such as as single environment variables or through files stored in volumes. These options provide various ways to manage and convey configuration data within Kubernetes pods efficiently.

## Secrets

ConfigMaps in Kubernetes are useful for storing configuration data in plain text format, but they are not ideal for sensitive information like passwords or keys. This is where Secrets come in, as they are designed to store confidential data securely in an encoded format. Managing Secrets involves two main steps: creation and injection into a Pod. There are two methods for creating a Secret: imperatively on the command line or using a declarative definition file. The imperative approach requires using the kubectl create secret generic command, where key-value pairs are directly specified. For a larger number of Secrets, utilizing the --from-file option allows inclusion of data stored in a file.

The declarative approach for Secrets involves creating a file with the API version, kind, metadata, and data fields. While Secrets are stored in an encoded format to offer an extra layer of protection compared to ConfigMaps, this encoding is by default not encrypted, meaning it's easily reversible. Encoding data using the base64 utility is straightforward, and decoding it can be done using the same tool with a decode option. This handling highlights the importance of not checking Secret definition files into version control systems like GitHub to avoid exposing sensitive information.

Once created, Secrets can be injected into Pods as environment variables through the envFrom property in the Pod definition file. Alternatively, Secrets can be mounted as files in a volume within the Pod, with each attribute of the Secret represented as a file. However, it's crucial to note that anyone with Pod or deployment creation rights in a namespace can access these Secrets. Therefore, implementing role-based access control (RBAC) to regulate access is critical.

Additionally, since Secrets and other Kubernetes data are not encrypted in ETCD, enabling encryption at rest is advisable. This involves creating an EncryptionConfiguration object and passing it as an option to the kube-apiserver. Using third-party secret management solutions like AWS, Azure, GCP providers, or Vault, offers enhanced security, as Secrets are stored externally and the providers manage the security measures. This reduces risks associated with storing sensitive data within the cluster's ETCD by default.

## Service Account

Service accounts in Kubernetes are essential components related to security aspects, such as authentication, authorization, and role-based access controls. Unlike user accounts used by humans, service accounts are meant for applications or machines interacting with the Kubernetes cluster, such as monitoring tools like Prometheus or build tools like Jenkins. To allow applications to communicate with the Kubernetes API, service accounts are used. For instance, a custom Kubernetes dashboard application would utilize a service account to authenticate and retrieve data from the API.

Creating a service account involves using the kubectl create serviceaccount command, automatically generating a token stored as a secret object associated with the service account. This token, accessible via the kubectl describe secret command, enables external applications to authenticate with the Kubernetes API by including it as a bearer token in requests. Applications hosted within the cluster can automatically access the service account token secret, mounted as a volume in the pod, simplifying the authentication process.

Every Kubernetes namespace features a default service account automatically mounted on pods. However, this default account has limited permissions and is normally unsuitable for applications requiring more extensive access. For specific requirements, modify the pod definition to include a different service account, allowing custom access controls through role-based mechanisms. Disabling automatic service account mounting is possible by setting the automountServiceAccountToken field to false in the pod spec.

Kubernetes releases 1.22 and 1.24 introduced significant enhancements to service account token handling. Before these updates, tokens lacked expiration and were not audience-bound, posing security risks. The TokenRequestAPI introduced in version 1.22 enables more secure, time-bound, and audience-bound tokens. Consequently, newly created pods, instead of using service account secret tokens, leverage dynamically generated tokens through the TokenRequestAPI, which are more secure and scalable.

Since version 1.24, service account creation no longer automatically generates secret tokens. To obtain a token, the kubectl create token command is used, producing a token with an expiration date. For cases where the previous method is required, secrets with non-expiring tokens can still be created, but they are not recommended due to security vulnerabilities. The TokenRequestAPI is favored as it offers tokens with a bounded lifetime and enhanced security characteristics, aligning with best practices in Kubernetes security management.
