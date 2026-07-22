# Kubernetes

Resources to assist developers to start getting familiar with Kubernetes.

## Glossary of terms

- **Kubernetes**: the orchestration service for deploying and managing resources, Sometimes abbreviated as **k8s**. It's more of a standard than a specific software executable; there are many different "Kubernetes" implementations out in the world.
- **Pod**: a unit of work, consisting of a set of Docker containers and their configuration. Each Pod contains one or more containers.
- **Node**: a computer running the Kubernetes node agent (kubelet). Executes Pods.
- **Cluster**: a logical collection of Nodes, managed by Kubernetes.
- **Service**: a networking abstraction which provides stable access to a set of dynamic, constantly-updating Pods.
- **Controller**: the mechanism by which Kubernetes orchestrates workloads. Each Controller will continuously perform actions to achieve a desired state (e.g. keep a container running in a Pod), and it will do this by observing specific variables (e.g. the Pod's liveness probe is healthy).
- **Namespace**: a logical subdivision of the Cluster. Provides scope for isolating groups of resources from each other (like DNS search domain).
- **Deployment**: a higher-level abstraction that manages Pods and ReplicaSets. Provides declarative updates for Pods and ReplicaSets.
- **ReplicaSet**: ensures that a specified number of Pod replicas are running at any given time.
- **ConfigMap**: an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or configuration files.
- **Secret**: similar to ConfigMap but specifically for storing sensitive information like passwords, OAuth tokens, and SSH keys.
- **Ingress**: an API object that manages external access to services in a cluster, typically HTTP. Provides routing rules to direct traffic to different services.
- **PersistentVolume (PV)**: a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.
- **PersistentVolumeClaim (PVC)**: a request for storage by a user. Pods consume PVCs as volumes.
- **DaemonSet**: ensures that all (or some) Nodes run a copy of a Pod. Used for cluster-wide services like logging or monitoring agents.
- **StatefulSet**: manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods. Used for stateful applications.
- **Job**: creates one or more Pods and ensures that a specified number of them successfully terminate. Used for batch processing tasks.
- **CronJob**: creates Jobs on a repeating schedule. Similar to cron jobs in Linux.
- **Helm**: a package manager for Kubernetes that packages multiple Kubernetes resources into a single logical package called a Chart.

## Learning & documentation

Resources that are useful / relevant for internal developers to learn just enough about Kubernetes to be productive.

### Beginner-friendly introductions

* [Introduction to Kubernetes on Azure](https://docs.microsoft.com/en-us/learn/paths/intro-to-kubernetes-on-azure/) - Learning path from Microsoft. Pretty thorough introduction to Docker, containers and AKS in general.
* [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/) - Interactive tutorials from the official Kubernetes documentation covering the fundamentals.
* [Kubernetes Workshop](https://github.com/ramitsurana/awesome-kubernetes#tutorials) - Collection of tutorials and workshops for learning Kubernetes.

### Official documentation

* [Kubernetes Documentation](https://kubernetes.io/docs/home/) - comprehensive, but not the best introductory learning resource. Has a pretty good set of interactive tutorials.
* [Kubernetes Concepts](https://kubernetes.io/docs/concepts/) - Detailed explanations of Kubernetes objects and concepts.
* [Kubernetes Reference](https://kubernetes.io/docs/reference/) - API references, CLI documentation, and setup guides.

### Video courses and tutorials

* [Kubernetes tutorial for beginners](https://www.youtube.com/watch?v=X48VuDVv0do) - TechWorld with Nana's comprehensive introduction (2+ hours).
* [Kubernetes fundamentals](https://www.udemy.com/course/learn-kubernetes/) - Udemy course covering core concepts (paid course).
* [Kubernetes the Hard Way (Video)](https://www.youtube.com/watch?v=7xI5tFvGNYg&list=PL4cUxeGkcC9gcyY1l2s8y3b7h3b7h3b7h) - Video walkthrough of building Kubernetes from scratch.

### Interactive learning platforms

* [Play with Kubernetes](https://labs.play-with-k8s.com/) - Free, online Kubernetes playground provided by Docker.
* [KillerCoda](https://killercoda.com/playgrounds/scenario/kubernetes) - Interactive Kubernetes scenarios in the browser.
* [Katacoda Kubernetes Scenarios](https://www.katacoda.com/courses/kubernetes) - Browser-based interactive Kubernetes tutorials.

## Core concepts deep dive

### Pods and workloads

* [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) - The smallest deployable units in Kubernetes.
* [Pod Lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/) - Understanding Pod states and transitions.
* [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) - Managing stateless applications.
* [StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) - Managing stateful applications.
* [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) - Running pods on every node.
* [Jobs and CronJobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/) - Running batch workloads.

### Networking

* [Service](https://kubernetes.io/docs/concepts/services-networking/service/) - Exposing applications within the cluster.
* [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) - Managing external access to services.
* [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) - Controlling traffic flow between pods.

### Storage

* [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/) - Storage volumes in Pods.
* [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) - Persistent storage in Kubernetes.
* [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/) - Dynamic provisioning of storage.

### Configuration

* [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/) - Storing configuration data.
* [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) - Storing sensitive data.
* [Environment variables](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) - Passing configuration to containers.

## kubectl references

* [Reference](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands) - Complete kubectl command reference.
* [Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) - Quick reference for common kubectl commands.
* [Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/) - Guidelines for using kubectl.
* [kubectl quick reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/) - Most common kubectl commands.

## Tools and utilities

### Package management

* [Helm](https://helm.sh/) - The package manager for Kubernetes.
* [Helm Hub](https://artifacthub.io/) - Find and share Helm charts.
* [Kustomize](https://kustomize.io/) - Kubernetes native configuration management.

## Other resources

* [Introduction to Kustomize](https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/) - Kustomize provides a solution for customizing Kubernetes resource configuration.
* [Complete Kustomize tutorial](https://glasskube.dev/blog/patching-with-kustomize/) - another good Kustomize reference.
* [Helm documentation](https://helm.sh/docs/) - Official Helm documentation and guides.
* [Kubernetes best practices](https://kubernetes.io/docs/setup/best-practices/) - Official best practices guide.
* [Kubernetes security best practices](https://kubernetes.io/docs/concepts/security/) - Security considerations and recommendations.

## Troubleshooting and debugging

* [Debug Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/) - Troubleshooting Pod issues.
* [Debug Services](https://kubernetes.io/docs/tasks/debug/debug-application/debug-service/) - Troubleshooting Service issues.
* [Debug Applications](https://kubernetes.io/docs/tasks/debug/debug-application/) - General application debugging techniques.
* [kubectl debug](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/) - Debug running pods with ephemeral containers.