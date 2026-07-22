# Kubernetes

## Glossary of terms

- Kubernetes: the orchestration service for deploying and managing resources, Sometimes abbreviated as **k8s**. It’s more of a standard than a specific software executable; there are many different "Kubenetes" implementations out in the world.
- Pod: a unit of work, consisting of a set of Docker containers and their configuration. Each Pod contains one of more containers.
- Node: a computer running the Kubernetes node agent (kubelet). Executes Pods.
- Cluster: a logical collection of Nodes, managed by Kubernetes.
- Service: a networking abstraction which provides stable access to a set of dynamic, constantly-updating Pods.
- Controller: the mechanism by which Kubernetes orchestrates workloads. Each A Controller will continuously perform actions to achieve a desired state (e.g. keep a container running in a Pod), and it will do this by observing specific variables (e.g. the Pod's liveness probe is healthy).
- Namespace: a logical subdivision of the Cluster. Provides scope for isolating groups of resources from each other (like DNS search domain).

## Learning & documentation

Resources that are useful / relevant for internal developers to learn just enough about Kubernetes to be productive.

* [Introduction to Kubernetes on Azure](https://docs.microsoft.com/en-us/learn/paths/intro-to-kubernetes-on-azure/) - Learning path from Microsoft. Pretty thorough introduction to Docker, containers and AKS in general.
* [Kubernetes Documentation](https://kubernetes.io/docs/home/) - comprehensive, but not the best introductory learning resource. Has a pretty good set of interactive tutorials.

## Other resources

* [Introduction to Kustomize](https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/) - Kustomize provides a solution for customizing Kubernetes resource configuration.
* [Complete Kustomize tutorial](https://glasskube.dev/blog/patching-with-kustomize/) - another good Kustomize reference.

# kubectl references

* Reference (https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
* Cheatsheet: https://kubernetes.io/docs/reference/kubectl/cheatsheet/
* Conventions: https://kubernetes.io/docs/reference/kubectl/conventions/