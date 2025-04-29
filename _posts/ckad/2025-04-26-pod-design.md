# Pod Design

### Table of contents:
- [Labels, Selectors and Annotations](#labels,-selectors-and-annotations)
- [Rolling Updates & Rollbacks in Deployments](#rolling-updates-&-rollbacks-in-deployments)
- [Deployment Strategies](#deployment-strategies)
- [Jobs](#jobs)
- [CronJobs](#cronjobs) 

## Labels, Selectors and Annotations

Labels and selectors are essential tools for grouping and filtering objects based on specific criteria. This concept can be applied to a variety of scenarios, such as
classifying species by attributes like class, kind, domestication, or color. Labels are properties attached to each item, allowing for flexible categorization, while
selectors enable filtering through conditions like "class equals mammal" or "color equals green." This technique is commonplace in digital environments, such as
tagging YouTube videos or applying filters in online stores.

In Kubernetes, labels and selectors are pivotal for managing various objects like pods, services, and deployments. As clusters grow and host numerous objects, labels
and selectors facilitate the organization and retrieval of these objects by type, application, or functionality. Developers can attach labels to each object according
to their needs, using the pod definition file's Metadata section to add labels in a key-value format. To view labeled objects, commands like kubectl get pods with
selector options are used, allowing easy and targeted retrieval of objects, such as finding pods belonging to a specific application.

The concept extends beyond simple retrieval. Kubernetes uses labels and selectors internally to connect different objects. For example, a replica set, which includes
multiple pods, employs labels within the pod definition and selectors in the replica set specification to ensure proper linkage. Beginners may often confuse label
placements; labels under the template section are for pods, while those at the top pertain to the replica set. A correctly configured selector field within the
replica set matches the labels on pods, ensuring seamless association and creation of the replica set.

Similarly, labels and selectors are crucial in services. When creating a service, selectors within the service definition file are employed to match labels on
desired pods within a replica set, enabling the service to interact effectively with the correct objects. Additionally, annotations serve a complementary role by
documenting details for informational purposes, such as tool versions, build information, or contact details, which may be used for integration tasks rather than
object filtering.

## Rolling Updates & Rollbacks in Deployments

Understanding rollouts and versioning in Kubernetes deployments is vital for managing application upgrades. When a new deployment is created, it triggers an initial
rollout, termed Revision 1. If the application is upgraded—such as when the container version updates—it initiates a new rollout resulting in Revision 2. Keeping
track of these revisions is crucial as it allows reverting to a previous deployment version if needed. Command-line tools like kubectl rollout status and `kubectl
rollout history` provide insights into the status and history of these deployments.

Kubernetes supports two primary deployment strategies: recreate and rolling update. Recreate involves shutting down all existing instances before deploying new ones,
which can lead to downtime as there is no overlap between application versions. The rolling update strategy, Kubernetes' default, avoids this issue by updating
instances one by one, thus keeping the application operational throughout the upgrade process.

Updating deployments may involve different tasks, including updating the application version or container image, adjusting labels, or changing replica counts.
While changes can be made directly in the deployment definition file and applied with the kubectl apply command, using commands like kubectl set image directly
updates the application image. However, caution is advised as configuration mismatches can arise between manual updates and definition files.

Deployment details, including the choice of deployment strategy, can be viewed via the kubectl describe deployment command. During a recreate strategy, logs will
show the old replica set scaling down to zero before the new one scales up. Conversely, a rolling update scales the old replica set down and the new one up
incrementally. Underlying deployment operations involve creating a new replica set to manage updated pods, all visible through `kubectl get replica sets`.

In scenarios where an upgrade leads to issues, Kubernetes enables rollbacks to previous revisions. The kubectl rollout undo command reverts PODs to their former
state by dismantling the new replica set and reinstating the old one. This procedural flexibility is evident when comparing replica set statuses before and after a
rollback, ensuring application stability. Essential commands include kubectl create for deployments, kubectl get deployments for listing, kubectl apply and `kubectl
set image` for updates, and kubectl rollout undo for rollbacks.

## Deployment Strategies

#### Blue Green

The blue-green deployment strategy involves maintaining the old and new versions of an application simultaneously. The old version is labeled as "blue," while the
new version is labeled as "green." Initially, all traffic is directed to the blue version, allowing tests to be performed on the green version without disrupting
current operations. Once the new version passes all tests, traffic is seamlessly switched to it, ensuring a smooth transition. This strategy is particularly
effective when implemented with service meshes like Istio. However, it can also be managed using native Kubernetes deployments and services.

In a typical blue-green deployment, the process starts with the original version of the application deployed as a service, labeled "blue deployment." A specific
label, such as version v1, is set on the pods within this deployment, and the service uses this label as a selector to route traffic to the corresponding pods.
Then, a second deployment, referred to as "green deployment," is established, hosting the new version of the application. The label on the pods in this deployment
is set to a new version identifier, such as v2, reflecting the upgrade.

To facilitate traffic rerouting after testing the new deployment, the label selector on the service is updated to match the version label of the green deployment.
This switch directs all service traffic to the pods in the new deployment, completing the transition from blue to green.

For example, consider a deployment named "myapp" with five replicas of version v1. A service is set to channel user traffic to this version, using labels to ensure
proper traffic routing. To upgrade using the blue-green strategy, a new deployment, say "myapp-green," is created with version 2.0 of the application, and the
version label changed to v2. After testing the green version, the service label selector is modified to v2, directing traffic to the new version’s pods. This
example illustrates how Kubernetes supports blue-green deployment strategies by leveraging its capabilities to efficiently manage label-based traffic routing and
update processes.

#### Canary

In a canary deployment strategy, a new version of an application is introduced alongside the existing version, with a small percentage of traffic initially routed to
this new version. The majority of the traffic continues to be directed to the older, stable version. This allows for tests to be conducted on the new version,
ensuring that everything functions correctly before proceeding with a full rollout. If the new version proves successful, it can be integrated into the primary
deployment using a methodlike a rolling upgrade strategy, followed by decommissioning the canary deployment.

To achieve this using Kubernetes, the original version of the application, referred to as the primary deployment, is deployed first. It consists of several pods,
for example, five, and a service is created to route traffic to these pods. Each pod in this deployment is tagged with a version label, such as v1. A second
deployment, the canary deployment, is then introduced, hosting the new version of the application. Initially, traffic remains directed entirely to version one.

The objective of this strategy is twofold: to direct traffic to both versions simultaneously and to ensure only a small fraction reaches the canary deployment. A
shared label, such as App set to front end, is used to solve the first challenge, enabling the service to route traffic to both deployments. However, without
additional configuration, traffic is distributed equally between the deployments. To limit traffic to the canary deployment, the number of pods in this deployment
is minimized, often to just one. This results in an uneven traffic distribution, with a larger percentage flowing to the primary deployment due to the greater
number of pods.

Despite its advantages, the basic canary strategy using Kubernetes deployments and services offers limited control over traffic distribution, as it relies on the
number of pods. Precise traffic control, such as routing a specific percentage, requires a substantial number of pods, highlighting the limitations of this approach.
Service meshes like Istio provide enhanced control, allowing for specific traffic percentages independent of pod count, making it possible to direct 1% of traffic in
one direction and 99% in another with minimal resources.

In practice, a canary deployment might involve first deploying the application as MyApp-Primary with a service selector using the label app set to front end. A
separate deployment, MyApp-Canary, utilizes an updated image, such as version 2.0, initially with one replica to limit incoming traffic. By ensuring consistent
labels and selectors across deployments, traffic is routed through the same service to both the primary and canary deployments, adapting and validating the new
application version progressively.

## Jobs

Containers serve a variety of workloads, from long-running tasks like web and application servers to short-lived sessions typical of batch processing, analytics, or
reporting. Simple web servers continue indefinitely until manually shut down, while batch workloads execute specific tasks—such as computations, analytics, or
report generation—and terminate upon completion. Initially, this concept is illustrated with Docker, showcasing how a container can perform a mathematical operation,
produce output, and exit with a status code indicating completion success.

Translating this process into Kubernetes involves creating a pod definition for the task. When a pod is initiated, it executes the assigned operation and enters a
completed state. However, Kubernetes' default behavior is to restart containers, driven by a restart policy set to "always." This setting reflects Kubernetes'
intent to keep applications alive. Modifying this policy to "never" or "on failure" prevents unnecessary restarts after a task finishes, aligning with the needs of
batch processing workloads.

For batch tasks requiring multiple pods for parallel data processing, Kubernetes employs jobs rather than replica sets. Jobs manage pods for task completion, not
continuous operation. A job is created using a definition file specifying batch settings, such as API version, job kind, and specifics under the "spec" section.
Once created with kubectl create, the job and its completion status can be verified. Successful completion is evident when pods show zero restarts, with output
accessible via the kubectl logs command. Deleting a job with kubectl delete job also removes associated pods.

For more complex jobs, such as image processing or report generation, outputs are typically stored or forwarded as per job requirements. By adjusting the
"completions" value, multiple pods can be run sequentially, aiming for a specified number of successful completions. In cases of pod failure, Kubernetes
automatically attempts to meet completion targets by creating new pods as needed. The "parallelism" setting further optimizes job execution by permitting
simultaneous pod creation, expediting the completion process by intelligently managing pod generation until success criteria are satisfied.

## CronJobs

A CronJob in Kubernetes is akin to a Crontab in Linux, providing a way to schedule jobs to run at specified intervals. This feature is particularly useful for tasks
that need to execute regularly, such as generating reports and sending emails. While a standard job created using the kubectl create command runs immediately, a
CronJob allows for periodic execution, ensuring the job runs exactly when needed.

To create a CronJob, you begin with a blank template. The API version for CronJobs currently stands at batch/v1beta1, and the kind is noted as CronJob with proper
capitalization. A descriptive name, such as "Reporting CronJob," helps identify it. Under the spec section, the schedule is specified using a Cron-like format,
allowing detailed timing specifications for when the job should execute. The job template follows, which contains the actual job definition, and all content from
the job's spec section is moved here.

The CronJob definition can become complex, as it contains three nested spec sections: one for the CronJob itself, one for the job, and one for the pod. Careful
attention is required to manage these sections properly. Once the CronJob configuration file is ready, the kubectl create command is used to establish it, and the
kubectl get CronJob command verifies its creation. The CronJob then autonomously manages the creation of jobs and pods according to the defined schedule, automating
and streamlining recurring tasks.
