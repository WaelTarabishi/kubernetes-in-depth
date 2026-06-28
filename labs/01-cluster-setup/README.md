# Lab 01 - Cluster Setup

## Objective

Set up a local Kubernetes cluster that can be reused for the rest of this learning repository. This lab teaches how to install the basic tooling, create a disposable cluster, and confirm that `kubectl` is talking to the correct environment.

## Theory

Kubernetes runs workloads on a cluster made up of control plane components and worker nodes. The control plane stores cluster state and coordinates scheduling, while worker nodes run the actual containers. In a learning environment, a local cluster created with `kind` or Minikube is useful because it is fast to recreate, cheap to run, and safe to break. `kubectl` connects to clusters through a kubeconfig file, so understanding contexts early prevents accidental commands against the wrong cluster.

## Prerequisites

- A machine with virtualization or container support and at least 4 GB of available memory
- A container runtime such as Docker Desktop, Docker Engine, or Podman
- `kubectl` installed and available in `PATH`
- A local cluster tool such as `kind` or Minikube
- Basic terminal familiarity

## Lab Steps

1. Install and verify the required tools:
   - `kubectl version --client`
   - `kind version` or `minikube version`
2. Create a local cluster. A simple `kind` example is:
   - `kind create cluster --name k8s-indepth`
3. Confirm that your current kubeconfig context points to the new cluster:
   - `kubectl config current-context`
4. Inspect the cluster and system workloads:
   - `kubectl cluster-info`
   - `kubectl get nodes -o wide`
   - `kubectl get pods -n kube-system`
5. Record how to clean up the environment when finished:
   - `kind delete cluster --name k8s-indepth`

## Verification

- `kubectl cluster-info`
- `kubectl get nodes -o wide`
- `kubectl get pods -n kube-system`
- `kubectl config current-context`

## What I Learned

Expected outcomes after completing this lab:

- I can explain the difference between the control plane and worker nodes.
- I can create and remove a local Kubernetes cluster safely.
- I can verify that `kubectl` is pointed at the intended cluster before continuing with other labs.

## Interview Questions

1. What is the role of the Kubernetes control plane?
   - It manages the cluster by maintaining the desired state, scheduling workloads, and coordinating all Kubernetes operations.
2. What is stored in a kubeconfig file?
   - Cluster information, user credentials, contexts, and the default cluster that `kubectl` connects to.
3. Why is a local cluster useful for learning Kubernetes?
   - It provides a safe, free environment to practice Kubernetes concepts without using cloud resources or incurring costs.
4. What is the difference between a node and a Pod?
   - A node is a machine (physical or virtual) that runs workloads, while a Pod is the smallest deployable unit in Kubernetes that contains one or more containers.
5. How do you confirm that `kubectl` is targeting the correct cluster?
   - Run `kubectl config current-context` to check the active context, and `kubectl cluster-info` or `kubectl get nodes` to verify the connected cluster.

## Common Mistakes

- Running `kubectl` commands without checking the active context
- Treating a local cluster as if it behaves exactly like a cloud-managed cluster
- Ignoring system Pods in `kube-system` when troubleshooting startup issues
- Deleting the cluster before exporting or saving any useful configuration notes

## References

- Kubernetes Overview: https://kubernetes.io/docs/concepts/overview/
- Install and Set Up `kubectl`: https://kubernetes.io/docs/tasks/tools/
- kind Documentation: https://kind.sigs.k8s.io/
- Minikube Documentation: https://minikube.sigs.k8s.io/docs/
