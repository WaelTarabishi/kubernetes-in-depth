# K8S-INDEPTH

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)
![Status](https://img.shields.io/badge/status-learning%20in%20public-blue)
![Kubernetes](https://img.shields.io/badge/focus-Kubernetes-326CE5)
![Platform Engineering](https://img.shields.io/badge/domain-Platform%20Engineering-4B5563)
![Documentation](https://img.shields.io/badge/docs-in%20progress-orange)

## Overview

`K8S-INDEPTH` is a structured Kubernetes study repository that documents a hands-on learning journey from core concepts to more production-oriented topics.

The goal of this repository is not to present finished production systems or inflated claims. It is a working study space for building practical Kubernetes knowledge through notes, labs, manifests, and supporting documentation.

## Learning Objectives

- Build a solid understanding of Kubernetes architecture and core resources
- Practice declarative infrastructure using YAML manifests
- Learn how workloads are deployed, exposed, configured, and secured
- Understand networking, storage, ingress, and Gateway API concepts
- Develop operational habits around verification, troubleshooting, and repeatability
- Progress toward cloud-managed Kubernetes concepts, including Amazon EKS

## Repository Structure

```text
K8S-INDEPTH/
|
|-- README.md
|-- CONTRIBUTING.md
|-- LICENSE
|-- .gitignore
|
|-- assets/
|   |-- images/
|   `-- gifs/
|
|-- diagrams/
|
|-- docs/
|   `-- README.md
|
|-- notes/
|   |-- architecture.md
|   |-- networking.md
|   |-- storage.md
|   |-- security.md
|   |-- helm.md
|   |-- gateway-api.md
|   `-- eks.md
|
|-- manifests/
|   |-- namespaces/
|   |-- deployments/
|   |-- services/
|   |-- configmaps/
|   |-- secrets/
|   |-- volumes/
|   |-- ingress/
|   |-- gateway-api/
|   `-- helm/
|
`-- labs/
    |-- 01-cluster-setup/
    |-- 02-namespaces/
    |-- 03-pods/
    |-- 04-replicasets/
    |-- 05-deployments/
    |-- 06-services/
    |-- 07-networking/
    |-- 08-configmaps/
    |-- 09-secrets/
    |-- 10-volumes/
    |-- 11-ingress/
    |-- 12-helm/
    |-- 13-gateway-api/
    `-- 14-eks/
```

## Technologies Used

- Kubernetes
- `kubectl`
- Docker
- kind or Minikube for local clusters
- Helm
- Gateway API
- Amazon EKS
- Markdown for documentation and study notes

## Learning Roadmap

1. Cluster setup and local development workflow
2. Namespaces and resource organization
3. Core workloads: Pods, ReplicaSets, and Deployments
4. Service discovery and networking fundamentals
5. Configuration management with ConfigMaps and Secrets
6. Persistent storage concepts and Kubernetes volume primitives
7. North-south traffic patterns with Ingress
8. Packaging and release management with Helm
9. Modern traffic management with Gateway API
10. Managed Kubernetes concepts with Amazon EKS

## Progress Checklist

### Repository Setup

- [x] Repository structure initialized
- [x] Root documentation scaffolded
- [x] Lab folders prepared
- [x] Topic notes scaffolded

### Kubernetes Learning Progress

- [ ] Local cluster bootstrap workflow completed
- [ ] Namespaces lab completed
- [ ] Pods lab completed
- [ ] ReplicaSets lab completed
- [ ] Deployments lab completed
- [ ] Services lab completed
- [ ] Networking lab completed
- [ ] ConfigMaps lab completed
- [ ] Secrets lab completed
- [ ] Volumes lab completed
- [ ] Ingress lab completed
- [ ] Helm lab completed
- [ ] Gateway API lab completed
- [ ] EKS lab completed

## How to Use This Repository

1. Clone the repository:

   ```bash
   git clone https://github.com/<your-username>/K8S-INDEPTH.git
   cd K8S-INDEPTH
   ```

2. Start with the root documentation:

   - Read this file for scope and roadmap
   - Review [docs/README.md](./docs/README.md)
   - Use the topic notes in `notes/` for concept summaries

3. Work through the labs in order:

   - Begin with `labs/01-cluster-setup`
   - Continue sequentially through the learning path
   - Add commands, outputs, screenshots, and takeaways as each lab is completed

4. Apply manifests as they are added and refined:

   ```bash
   kubectl apply -f manifests/namespaces/namespace.yaml
   ```

5. Verify resources and document findings:

   ```bash
   kubectl get all -A
   kubectl describe namespace learning
   ```

## Required Software

Install the following tools before running the labs:

- Git
- Docker Desktop or Docker Engine
- `kubectl`
- kind or Minikube
- Helm
- AWS CLI for EKS-related sections
- A code editor such as VS Code or IntelliJ IDEA

## Current Scope

This repository is intentionally documentation-first at this stage. It currently focuses on structure, study notes, and lab scaffolding. Kubernetes manifests will be added gradually as concepts are practiced, rather than generated in bulk without validation.

## Future Goals

- Expand each lab with validated commands, expected outputs, and troubleshooting notes
- Add architecture diagrams for core control plane and networking concepts
- Record comparisons between local clusters and managed Kubernetes services
- Add Helm chart exercises and Gateway API examples after the fundamentals are complete
- Document EKS-specific operational considerations and lessons learned

## Contributing

Contributions are welcome if they improve clarity, correctness, or structure.

Please read [CONTRIBUTING.md](./CONTRIBUTING.md) before opening a pull request.

## License

This repository is licensed under the MIT License.

See [LICENSE](./LICENSE) for details.
