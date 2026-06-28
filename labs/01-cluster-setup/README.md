# Lab 01 - Cluster Setup

## Objective

Set up a local Kubernetes cluster for the rest of the labs and verify that `kubectl` is pointed at the correct environment.

## Prerequisites

- `kubectl` installed and available in `PATH`
- One local cluster tool: `kind` or Minikube
- A container runtime such as Docker Desktop, Docker Engine, or Podman
- Enough CPU and memory to run a local cluster
- Basic terminal knowledge

## Key Concepts

- A Kubernetes cluster contains control plane components and one or more worker nodes.
- `kubectl` talks to a cluster by reading kubeconfig contexts.
- A local cluster is useful for learning because it is easy to create, break, and recreate.
- `kind` runs Kubernetes nodes as containers, while Minikube creates a local VM or container-based cluster depending on the driver.

## Lab Steps

1. Check that `kubectl` is installed.
   - What we are doing: Confirm the Kubernetes CLI is available before creating a cluster.
   - Command:
     ```bash
     kubectl version --client
     ```
   - Short explanation: Shows the installed client version without requiring a working cluster connection.

2. Check your local cluster tool.
   - What we are doing: Verify whether you are using `kind` or Minikube.
   - Command:
     ```bash
     kind version
     ```
     or
     ```bash
     minikube version
     ```
   - Short explanation: Confirms the cluster bootstrap tool is installed and ready.

3. Create a local cluster.
   - What we are doing: Start a disposable Kubernetes cluster named `k8s-indepth`.
   - Command:
     ```bash
     kind create cluster --name k8s-indepth
     ```
     or
     ```bash
     minikube start
     ```
   - Short explanation: Creates a local Kubernetes control plane and worker node environment for practice.

4. Verify cluster connectivity.
   - What we are doing: Confirm `kubectl` can talk to the cluster API server.
   - Command:
     ```bash
     kubectl cluster-info
     ```
   - Short explanation: Prints the cluster control plane endpoints that `kubectl` is currently using.

5. Verify the nodes.
   - What we are doing: Check that the cluster node is ready.
   - Command:
     ```bash
     kubectl get nodes -o wide
     ```
   - Short explanation: Lists the nodes and shows whether they are in the `Ready` state.

6. Check the active kubeconfig context.
   - What we are doing: Make sure future commands target the correct cluster.
   - Command:
     ```bash
     kubectl config current-context
     ```
   - Short explanation: Prints the active kubeconfig context currently used by `kubectl`.

7. Inspect system Pods.
   - What we are doing: Verify that cluster system components are running.
   - Command:
     ```bash
     kubectl get pods -n kube-system
     ```
   - Short explanation: Shows core services such as DNS and networking components that support the cluster.

8. Delete the cluster when finished.
   - What we are doing: Clean up the local cluster after testing.
   - Command:
     ```bash
     kind delete cluster --name k8s-indepth
     ```
     or
     ```bash
     minikube delete
     ```
   - Short explanation: Removes the local learning cluster and frees local resources.

## YAML Examples

This lab does not require Kubernetes manifest files because cluster setup is done with local tools such as `kind` or Minikube rather than `kubectl apply -f`.

## Verification

```bash
kubectl version --client
kubectl cluster-info
kubectl get nodes -o wide
kubectl config current-context
kubectl get pods -n kube-system
```

## Expected Output

- `kubectl version --client` should print a client version.
- `kubectl get nodes -o wide` should show at least one node in the `Ready` state.
- `kubectl config current-context` should show the local cluster context such as `kind-k8s-indepth` or `minikube`.
- `kubectl get pods -n kube-system` should show system Pods such as CoreDNS and the network plugin.

## Troubleshooting

- `kubectl` not found:
  Install `kubectl` and make sure it is in `PATH`.
- `kind` or `minikube` command not found:
  Install the missing local cluster tool before continuing.
- Cluster creation fails:
  Check that Docker Desktop, Docker Engine, or the Minikube driver is running.
- Nodes stay `NotReady`:
  Inspect `kubectl get pods -n kube-system` and `kubectl describe node <node-name>` for DNS or network plugin issues.
- Wrong cluster context:
  Run `kubectl config get-contexts` and switch with `kubectl config use-context <context-name>`.

## Cleanup

```bash
kind delete cluster --name k8s-indepth
```

Or, if you used Minikube:

```bash
minikube delete
```

## Key Takeaways

- `kubectl` depends on kubeconfig context, so context checks should become a habit.
- A local cluster is enough for learning core Kubernetes workflows.
- Verifying nodes and system Pods is the fastest basic health check after cluster creation.

## Interview Questions

1. What does `kubectl config current-context` show?
   It shows which kubeconfig context `kubectl` is currently using.
2. Why is a local cluster useful for learning Kubernetes?
   It gives you a safe environment to practice without using cloud infrastructure.
3. What does `kubectl get nodes` help you verify?
   It confirms that cluster nodes exist and whether they are ready to run workloads.
4. What is the difference between `kind` and Minikube at a high level?
   `kind` runs Kubernetes nodes as containers, while Minikube creates a local cluster using a driver such as Docker or a VM.
