# DevOps best practices

This page is a work in progress to collect and record examples and templates for development and operational best practices for application workloads deployed to AKS clusters. These practices are aimed at improving information security and operational reliability. All recorded items should include references and links to current worked examples, and may evolve over time.

Useful references:

- Docker security cheatsheet: <https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html>
- Docker container security best practices: <https://blog.gitguardian.com/how-to-improve-your-docker-containers-security-cheat-sheet/>
- Storage considerations for AKS: <https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/app-platform/aks/storage>

## General design principles

- Build containerised applications to run with the least amount of privilege required to function, and to not have administrative rights to the container in which they run (i.e. no local root privilege).
- Architect application workloads to eliminate local state data as much as possible, allowing Kubernetes to efficiently migrate pods between available nodes.
- Minimise installation of unnecessary executables and software within running container images to improve security by reducing the attack surface and to reduce the size of images.
- Ensure that workloads have a well-defined single purpose, i.e. do not deploy resources which carry out multiple functions (e.g. a web-server that also carries out periodic jobs).
- Make use of health probes (startup/liveness/readiness mechanisms) so that Kubernetes can detect whether an application is started, healthy and ready to serve requests.

## Pods run with a security context that disallows privilege escalation

A Kubernetes pod has the capability of gaining more privileges than its parent process. In certain circumstances this be lead to security issues such as container escapes. In addition, Kubernetes allows a number of additional [capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html) to be granted to the running process without granting the full capabilities of the root user. According to the security [Principle of Least Privilege](https://www.crowdstrike.com/cybersecurity-101/principle-of-least-privilege-polp/), a process should be granted only the capabilities required to do its job.

References:

- <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/>
- <https://www.crowdstrike.com/cybersecurity-101/principle-of-least-privilege-polp/>

### Worked example

The IT Assets Deployment resource runs with a defined security context set via its Kustomize resource manifest. The security context specifies that privilege escalation is disallowed, and drops all additional capabilities that could be granted to the running process as they are not required for operation (`privileged: false`, `allowPrivilegeEscalation: false` and `capabilities.drop: [- ALL]`).

- <https://github.com/dbca-wa/it-assets/blob/master/kustomize/base/deployment.yaml>

## Containers run using an unprivileged user

By default, the process inside a Docker container runs as the **root** user. Configuring the container to use an unprivileged account is the best way to prevent privilege escalation attacks. Our AKS infrastructure has a number of policies applied to discourage the use of privileged containers (root user). In addition, Kubernetes resources can be restricted to running as an explicit user and group ID in order to guarantee that a pod's process runs with a well-defined set of permissions.

References:

- <https://docs.docker.com/develop/develop-images/instructions/#user>
- <https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html#rule-2-set-a-user>

### Worked example

IT Assets run a WSGI server (`gunicorn`) using Python. The application has no requirement to run Python as a privileged user, so the Dockerfile defines an unprivileged user with an explicit UID & GID so that builds are deterministic. The image uses this account on startup via the [USER instruction](https://docs.docker.com/engine/reference/builder/#user). In addition, the Kustomize resource manifest for the deployment sets a security context that explicitly sets the running process to not use the root user, and to ensure that the user and group ID created in the Dockerfile are the ones in use (`runAsNonRoot: true` and `runAsUser: 1000`).

- <https://github.com/dbca-wa/it-assets/blob/master/Dockerfile>
- <https://github.com/dbca-wa/it-assets/blob/master/kustomize/base/deployment.yaml>

## Pods have startup/liveness/readiness probes

Container workloads can define startup, liveness and/or readiness probes to allow the k8s orchestration service to confirm that they have started and are running correctly. This assists with system reliability and allows for self-healing behaviour if containers or nodes become unstable. Note: Kubernetes does not use the Dockerfile [HEALTHCHECK instruction](https://docs.docker.com/reference/dockerfile/#healthcheck).

References:

- <https://learn.microsoft.com/en-us/azure/application-gateway/ingress-controller-add-health-probes>
- <https://mannes.tech/container-healthiness/>

### Worked example

IT Assets defines a liveness probe (to confirm the WSGI server has started and is available to serve HTTP requests) and a readiness probe (to confirm that a valid database connection has been made). This involves defining a valid URL path for each probe to which k8s can make a HTTP request. These URLs and views could be implemented in several ways, but IT Assets defines a simple Middleware class to intercept and process those HTTP requests. See `startupProbe`, `livenessProbe` and `readinessProbe`.

- <https://github.com/dbca-wa/it-assets/blob/master/kustomize/base/deployment.yaml>
- <https://github.com/dbca-wa/it-assets/blob/master/itassets/middleware.py>

## Pods use a read-only root filesystem

Most containers should be ephemeral and thus mostly stateless. Minimise the attack surface of your overall application by limiting the mounted filesystem to be read-only. Individual applications may require read access to specific directory paths (e.g. `/var/run`), therefore implementing this is context-specific.

### Worked example

Resource Tracking system runs a WSGI service using Gunicorn which makes use of the the `/tmp` directory for workers. Its deployment workload defines a temporary in-memory filesystem mounted at that path for the lifetime of a running container. All other parts of the root filesystem are read only by setting the `securityContext.readOnlyRootFilesystem: true` value for the deployment.

<https://github.com/dbca-wa/resource_tracking/blob/master/kustomize/base/deployment.yaml>

## Cluster workloads utilise Horizontal Pod Autoscaling and Pod Disruption Budgets

In Kubernetes, a Horizontal Pod Autoscaler automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand. Horizontal scaling means that the response to increased load is to deploy more Pods. This can help improve the reliability and performance of an application in response to periods of high demand. In addition, this can help to reduce AKS resource usage during periods of low demand and reduce the ongoing costs for the department.

Separately, a Pod Disruption Budget (PDB) can be used to define the minimum number of pods that must be running for a specified application. It can be used to minimise outages during voluntary disruptions such as node upgrades. A PDB is an important tool that allows the Kubernetes scheduler to maintain a minimum level of availability for an application. Note that using a PDB is only appropriate for workloads having more than one pod replica.

References:

- <https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/>
- <https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/>
- <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>

### Notes

Horizontal Pod Autoscaling may not be appropriate for use in all contexts. For example, if a workload utilises unique local state data that needs to be preserved (e.g. a Redis cache StatefulSet workload using a local in-memory data store), then horizontal scaling is not a suitable approach to load management as newly-created pods will not share that state data.

### Worked example

The Caddy service runs as a Deployment with default number of two replicas. The Deployment resource sets resource requests for CPU and memory per replica. A HorizontalPodAutoscaler resource is also defined referencing the Deployment resource, setting a minimum of two and a maximum of 10 replicas. The scaling metric used is 500% of the CPU request value (10m). The autoscaler will scale the number of Deployment replicas (to a maximum of 10) to bring the _average_ CPU utilisation of _all_ running pods below 500% of the requested CPU (i.e. 50m).

In addition, a PDB has been defined for the Deployment that sets the minimum number of available replicas to 1. This assists the Kubernetes scheduler to ensure that a minimum of 1 running replica should always be available within the cluster (e.g. during node drain events).

- <https://github.com/dbca-wa/caddy/blob/master/kustomize/base/deployment.yaml>
- <https://github.com/dbca-wa/caddy/blob/master/kustomize/base/deployment_hpa.yaml>
- <https://github.com/dbca-wa/caddy/blob/master/kustomize/overlays/prod/pdb.yaml>
