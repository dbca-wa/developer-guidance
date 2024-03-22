# DevOps best practices

This page is a WiP to collect and record examples and templates for development and operational best practices for application workloads deployed to AKS clusters. These practices are aimed at improving information security and operational reliability. All recorded items should include references and links to current worked examples, and may evolve over time.

Useful references:

- <https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html>
- <https://blog.gitguardian.com/how-to-improve-your-docker-containers-security-cheat-sheet/>
- Storage considerations for AKS: <https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/app-platform/aks/storage>

# Cluster pods run with a security context that disallows privilege escalation

A Kubernetes pod has the capability of gaining more privileges than its parent process. In certain circumstances this be lead to security issues such as container escapes. In addition, Kubernetes allows a number of additional [capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html) to be granted to the running process without granting the full capabilities of the root user. According to the security [Principle of Least Privilege](https://www.crowdstrike.com/cybersecurity-101/principle-of-least-privilege-polp/), a process should be granted only the capabilities required to do its job.

References:

- <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/>
- <https://www.crowdstrike.com/cybersecurity-101/principle-of-least-privilege-polp/>

## Worked example

The IT Assets Deployment resource runs with a defined security context set via its Kustomize resource manifest. The security context specifies that privilege escalation is disallowed, and drops all additional capabilities that could be granted to the running process as they are not required for operation.

- <https://github.com/dbca-wa/it-assets/blob/master/kustomize/base/deployment.yaml>

# Cluster containers run using an unprivileged user

By default, the process inside a Docker container runs as the **root** user. Configuring the container to use an unprivileged account is the best way to prevent privilege escalation attacks. Our AKS infrastructure has a number of policies applied to discourage the use of privileged containers (root user). In addition, Kubernetes resources can be restricted to running as an explicit user and group ID in order to guarantee that a pod's process runs with a well-defined set of permissions.

References:

- <https://docs.docker.com/develop/develop-images/instructions/#user>
- <https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html#rule-2-set-a-user>

## Worked example

IT Assets run a WSGI server (gunicorn) using Python. The application has no requirement to run Python as a privileged user, so the Dockerfile defines an unprivileged user with an explicit UID & GID so that builds are deterministic. The image uses this account on startup via the [USER instruction](https://docs.docker.com/engine/reference/builder/#user). In addition, the Kustomize resource manifest for the deployment sets a security context that explicitly sets the running process to not use the root user, and to ensure that the user and group ID created in the Dockerfile are the ones in use.

- <https://github.com/dbca-wa/it-assets/blob/master/Dockerfile>
- <https://github.com/dbca-wa/it-assets/blob/master/kustomize/base/deployment.yaml>

# Cluster containers have startup/liveness/readiness probes

Container workloads can define startup, liveness and/or readiness probes to allow the k8s orchestration service to confirm that they have started and are running correctly. This assists with system reliability and allows for self-healing behaviour if containers or nodes become unstable. Note: Kubernetes does not use the Dockerfile [HEALTHCHECK instruction](https://docs.docker.com/reference/dockerfile/#healthcheck).

References:

- <https://learn.microsoft.com/en-us/azure/application-gateway/ingress-controller-add-health-probes>
- <https://mannes.tech/container-healthiness/>

## Worked example

IT Assets defines a liveness probe (to confirm the WSGI server has started and is available to serve HTTP requests) and a readiness probe (to confirm that a valid database connection has been made). This involves defining a valid URL path for each probe to which k8s can make a HTTP request. These URLs and views could be implemented in several ways, but IT Assets defines a simple Middleware class to intercept and process those HTTP requests.

- <https://github.com/dbca-wa/it-assets/blob/master/itassets/middleware.py>

# Cluster containers use a read-only root filesystem

Containers should be ephemeral and thus mostly stateless. Minimise the attack surface of your overall application by limiting the mounted filesystem to be read-only. Individual applications may require read access to specific directory paths (e.g. `/var/run`), therefore implementing this is context-specific.

## Worked example

Resource Tracking system runs a WSGI service using Gunicorn which makes use of the the `/tmp` directory for workers. Its deployment workload defines a temporary in-memory filesystem mounted at that path for the lifetime of a running container. All other parts of the root filesystem are read only by setting the `securityContext.readOnlyRootFilesystem` value for the deployment.

- <https://github.com/dbca-wa/resource_tracking/blob/master/kustomize/base/deployment.yaml>

