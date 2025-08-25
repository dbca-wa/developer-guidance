# Kustomize

## Overview

- Kustomize is Kubernetes' own tool for generating k8s resources from plain YAML, without any domain-specific language.
- It takes in file content and emits text (YAML) which targets Kubernetes.
  Reference: <https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/>

## The value proposition

- Scripted, declarative deployments - fast, repeatable, consistent.
- Deployment definitions can be committed to the Git repo and live with the project code. Your code repository then becomes the source of truth for your deployment infrastructure.
- Change control becomes easier and more standardised. Outsource system deployments to non-technical custodians (Infrastructure, junior developers, etc.) via a checklist of instructions.
- Portability - move your apps between clusters easily (Dev -> UAT -> Prod) with trivial effort. Disaster recovery becomes MUCH easier and faster.
- Removes the dependency on Rancher for deployments / changes.

## Prerequisites

- A running Kubernetes cluster.
- `kubectl` installed locally and configured for the cluster.

## Installation

If not already installed with kubectl (run `kubectl version` to check), the easiest method of installation is via [downloading a pre-compiled binary](https://kubectl.docs.kubernetes.io/installation/kustomize/binaries/). The following will download and install the binary in a Linux Bash shell:

```bash
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash
```

## Description (kustomization.yaml)

- Kustomize takes in YAML resource definitions, customises them, and outputs YAML resource manifests that are applied to a cluster.
- A Kustomize resource definition is just a directory containing a `kustomization.yaml` file. This file is a specification of the Kubernetes Resource Model (KRM).
- At it's simplest, the `kustomization.yaml` file lists the Kubernetes resources to be managed by Kustomize, e.g.

```yaml
resources:
  - <Path or URL>
  - <Path or URL>
```

- Path can be a path to a file or directory (relative to the `kustomization.yaml`), or an internet URL.
- As well as a list of resource, the `kustomization.yaml` file defines customisations to make to those resources, e.g.

```yaml
nameSuffix: -prod
labels:
  businessUnit: OIM
```

- The example above will apply the label `businessUnit` with `OIM` and add the suffix `-uat` to all created resources (there are MANY more transformation options).
- Kustomization resources can be built from "base" resources with changes that are "overlaid" (or patched) on top of them.
- Concept: we have a set of base resources, a UAT overlay and a prod overlay. Example directory structure:

```bash
kustomize/
   ├── base/
   │   ├── kustomization.yaml
   │   ├── deployment.yaml
   │   └── service.yaml
   └── overlays/
      └── uat/
          ├── kustomization.yaml
          ├── deployment_patch.yaml
          ├── service_patch.yaml
          └── ingress.yaml
      └── prod/
          ├── kustomization.yaml
          ├── deployment_patch.yaml
          ├── service_patch.yaml
          └── ingress.yaml
```

References:

- Basic intro: <https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/>
- Kustomize reference: <https://kubectl.docs.kubernetes.io/references/kustomize/>
- A good overview of the `kustomization.yaml` file: <https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/>

## Self-paced Kustomize tutorial

### Prerequisites

- Shell environment with a newish version of kubectl installed (1.29+), which will have Kustomize included.
- Configure kubectl to have a context per cluster you need to work with (az-aks-oim03, az-aks-prod01, etc.)
  - Defaults to using`~/.kube/config`. Set `KUBECONFIG` variable to use something else.
  - Easiest to get your personal kubeconfig YAML from Rancher, and then merge/flatten one from each cluster into a single `config`.
- Recommended: some kind of YAML linter.

`kubectl` commands for testing your configuration is set up:

```bash
kubectl config
kubectl config view
kubectl config current-context
kubectl config use-context <cluster-name>
kubectl cluster-info
kubectl get namespaces
```

References:

- <https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/>
- <https://able8.medium.com/how-to-merge-multiple-kubeconfig-files-into-one-36fc987c2e2f>

### Step 0 - Intro

1. Clone the following Git repository and cd into it: <https://github.com/dbca-wa/kustomize-tutorial>
1. Run `git checkout 00-intro`
1. Run `kubectl kustomize overlays/uat`, noting the YAML output.
1. Run `kubectl apply -k overlays/uat` and watch the **default** namespace on the cluster, noting the created Deployment workload (nginx-deployment).
1. Run `kubectl delete -k overlays/uat` to clean up the workload.
1. Run `kubectl apply -k overlays/prod` to review the Deployment workload and note differences from the UAT one (tagged image version, resource request).
1. Run `kubectl delete -k overlays/prod` to clean up.

Learning outcomes:

- Resource deployment / removal.
- Differences between overlays based on patches.

### Step 1 - Refinements

1. Run `git checkout 01-refinements`
1. Run `kubectl apply -k overlays/uat` and watch the deployment resource start, having the -uat name suffix.
1. Run `kubectl apply -k overlays/prod`, and note the difference in resource name.
1. Take a look at the configuration of each resource and note differences.
1. Clean up the resources.

Learning outcomes:

- Extended variables in deployment resource (environment variable, restartPolicy, imagePullPolicy, etc.)
- **nameSuffix** in kustomization.yaml (<https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/namesuffix/>).

### Step 2 - Service & label selectors

1. Run `git checkout 02-service`
1. Run `kubectl apply -k overlays/uat` and then `kubectl apply -k overlays/prod`.
1. Examine the created deployments and services, noting the different label selectors that have been added to each resource (app, variant).
1. Clean up the resources.

Learning outcomes:

- Setting `labels` in kustomizations.yaml adds labels to all resources (<https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/labels/>).
- Setting `includeSelectors: true` also adds selectors on those labels.

### Step 3 - Secret values

1. Run `git checkout 03-secrets`
1. Inside the overlays/uat directory, create a `.env` file containing the line: `SECRET_VALUE=uat_secret-value`
1. Run `kubectl apply -k overlays/uat` and note the opaque secret that has been created along with the deployment and service.
1. Start a shell session in the Nginx workload and run `echo $SECRET_VALUE` to confirm that the value is present as an environment variable.
1. Repeat the steps above for the prod overlay, if desired.
1. Clean up the resources.

Learning outcomes:

- Use a **secretGenerator** to generate a resource from values in a file: <https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/secretgenerator/>
- Observe how to set environment variables inside deployments using key values in opaque secrets.
