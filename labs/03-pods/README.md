# Lab 03 - Pods

## Objective

Understand Pods as the smallest deployable unit in Kubernetes. This lab teaches how to create a standalone Pod, inspect its lifecycle, and troubleshoot it with `kubectl`.

## Theory

A Pod is a wrapper around one or more tightly coupled containers that share the same network namespace, IP address, and optional storage volumes. Pods are scheduled onto nodes as a single unit. A standalone Pod is useful for learning, debugging, or short-lived tasks, but long-running applications are usually managed by higher-level controllers such as Deployments. Pod lifecycle states such as `Pending`, `Running`, `Succeeded`, and `Failed` help explain what the scheduler and kubelet are doing.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- Basic familiarity with containers and images

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace pods-lab`
2. Create a standalone Pod:
   - `kubectl run demo-pod --image=nginx:1.25 --restart=Never -n pods-lab`
3. Watch the Pod move through its lifecycle:
   - `kubectl get pods -n pods-lab -w`
4. Inspect details and events:
   - `kubectl describe pod demo-pod -n pods-lab`
5. View logs and access the container:
   - `kubectl logs demo-pod -n pods-lab`
   - `kubectl exec -it demo-pod -n pods-lab -- sh`
6. Delete and recreate the Pod to observe that it is not self-healing without a controller:
   - `kubectl delete pod demo-pod -n pods-lab`

## Verification

- `kubectl get pod demo-pod -n pods-lab -o wide`
- `kubectl describe pod demo-pod -n pods-lab`
- `kubectl logs demo-pod -n pods-lab`
- `kubectl exec demo-pod -n pods-lab -- hostname`

## What I Learned

Expected outcomes after completing this lab:

- I can explain what a Pod is and why Kubernetes schedules it as a unit.
- I can inspect Pod events, logs, and status fields during troubleshooting.
- I understand why standalone Pods are usually replaced by controllers for real workloads.

## Interview Questions

1. What is a Pod in Kubernetes?
   - A Pod is the smallest deployable unit in Kubernetes that encapsulates one or more containers sharing the same network and storage.
2. Why do containers inside the same Pod share a network namespace?
   - So they can communicate using `localhost` and share the same IP address and ports.
3. What is the difference between a Pod and a container?
   - A container runs an application, while a Pod is a Kubernetes object that hosts one or more containers and provides shared networking and storage.
4. What happens if a standalone Pod is deleted?
   - It is permanently removed and is **not** recreated automatically because no controller manages it.
5. Which `kubectl` commands are most useful when a Pod is not starting?
   - `kubectl get pods`, `kubectl describe pod <pod-name>`, and `kubectl logs <pod-name>`.

## Common Mistakes

- Using standalone Pods for applications that should be controller-managed
- Assuming a deleted Pod will be recreated automatically
- Ignoring the `Events` section in `kubectl describe`
- Confusing container logs with Pod status information

## References

- Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- Pod Lifecycle: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
- Debug Running Pods: https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/
