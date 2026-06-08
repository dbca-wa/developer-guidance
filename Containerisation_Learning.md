# Containerisation & Orchestration learning syllabus

A systematic learning path for the full stack of running DBCA application services in containers in an orchestrated environment.

After completing this syllabus, you will:

- Build and optimize container images
- Run multi-container applications locally
- Deploy scalable apps to Kubernetes
- Manage environments cleanly using Kustomize
- Understand production-grade DevOps workflows

**How to approach this syllabus**: use the phases and goals as a roadmap, not a prescription. Pass over topics that you already know, but ensure that you have an understanding about all of them.

## Phase 1: Containers & Docker fundamentals

**Goal:** Understand containerisation and learn Docker basics.

### Topics

- What is containerisation? (vs VMs)
- Docker architecture (daemon, CLI, images, containers)
- Working with Docker registries e.g. Docker Hub (pull, push)
- Creating and running containers
- Writing Dockerfiles:
  - Base images
  - Layers and caching
  - `COPY` vs `ADD`
  - `ENV`, `CMD`, `ENTRYPOINT`
- Managing containers:
  - Logs, `exec`, `inspect`
- Volumes & persistent storage
- Networking in Docker (bridge, host, ports)

### Practice tasks

- Dockerize a simple web app (e.g., Python Flask)
- Persist data using volumes
- Build and share an image on Docker Hub or the GitHub Container Repository

## Phase 2: Intermediate Docker

**Goal:** Improve image quality and container workflows.

### Topics

- Multi-stage builds (optimizing images)
- Image layering and size reduction
- Environment variables and secrets
- Debugging containers
- Docker security basics (least privilege, image scanning basics)

### Practice tasks

- Optimize an existing Dockerfile
- Run multiple containers and connect them manually (database, webserver)

## Phase 3: Docker Compose

**Goal:** Manage multi-container applications easily.

### Topics

- Why Docker Compose
- `docker-compose.yml` structure
- Services, networks, volumes
- Environment variables in Compose
- `depends_on` and startup order
- Scaling services (basic)

### Practice tasks

- Multi-container app:
  - Web app + database (e.g. PostgreSQL + Flask app)
- Replace manual docker commands with Compose
- Simulate a local dev environment

## Phase 4: Containerisation best practices

**Goal:** Prepare for production-grade hosting.

### Topics

- Configuration vs code separation (environment variables, mounted file config)
- 12-Factor App principles
- Logging strategies
- Health checks
- Stateless vs stateful apps
- Intro to CI/CD with containers (automated image builds, unit testing, etc.)

## Phase 5: Kubernetes fundamentals

**Goal:** Understand orchestration and cluster management.

### Topics

- Why Kubernetes (what problem does it solve)?
- Kubernetes architecture (high-level concepts):
  - Control plane
  - Nodes, kubelet, etcd
- Core primitives:
  - Pods
  - ReplicaSets
  - Deployments
  - Services (ClusterIP, NodePort, LoadBalancer)
- kubectl basics

### Practice tasks

- Deploy an app to Rancher
- Scale a deployment
- Expose services internally and externally

## Phase 6: Kubernetes intermediate concepts

**Goal:** Build real-world cluster skills.

### Topics

- ConfigMaps & Secrets
- Volumes & persistent volumes (PV/PVC)
- Namespaces
- Resource requests & limits
- Rolling updates & rollbacks
- Liveness & readiness probes
- Ingress controllers

### Practice tasks

- Deploy a multi-tier application
- Use ConfigMaps for configuration
- Add readiness/liveness probes

## Phase 7: Kubernetes advanced concepts

**Goal:** Production readiness.

### Topics

- Horizontal Pod Autoscaling (HPA)
- Observability basics:
  - Logs, metrics (Prometheus/Grafana intro)
- RBAC (Role-Based Access Control)
- Security (Pod Security Standards)
- Helm basics (optional)

## Phase 8: Kustomize configuration management

**Goal:** Manage Kubernetes manifests cleanly without templating.

### Topics

- What Kustomize is and why it matters
- Base vs overlays structure
- `kustomization.yml`
- Patches (strategic merge, JSON patches)
- ConfigMap/Secret generators
- Environment-based deployments (dev/staging/prod)

### Practice tasks

- Convert raw YAML into Kustomize structure
- Create dev/staging/prod overlays
- Modify replicas, images, configs per environment

## Phase 9: Integration & real-world workflow

**Goal:** Combine everything into a deployable system.

### Topics

- CI/CD pipeline:
  - Build Docker images
  - Push to registry
  - Deploy via Kubernetes manifests/Kustomize

### Capstone project

- Build, deploy and manage a system in our Kubernetes environment.
  - Dockerize services
  - Use Compose for local dev
  - Deploy to Kubernetes
  - Manage configs with Kustomize
