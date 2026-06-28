# Lab 03 - Pods

## Objective

Understand what a Pod is, create one imperatively and declaratively, and use `kubectl` to inspect logs, status, and events.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured correctly
- The `learning` namespace from Lab 02

## Key Concepts

- A Pod is the smallest deployable unit in Kubernetes.
- A Pod can contain one or more containers that share network and storage resources.
- Standalone Pods are useful for learning and debugging, but controllers such as Deployments are better for long-running applications.
- `kubectl describe`, `kubectl logs`, and `kubectl exec` are core troubleshooting commands for Pod work.

## Lab Steps

1. Create a Pod imperatively.
   - What we are doing: Start a simple nginx Pod directly from the CLI.
   - Command:
     ```bash
     kubectl run nginx-pod --image=nginx:1.25 --restart=Never -n learning --labels="app=nginx,tier=frontend"
     ```
   - Short explanation: Creates a standalone Pod named `nginx-pod` in the `learning` namespace.

2. Inspect the Pod status.
   - What we are doing: Watch the Pod move into the `Running` state.
   - Command:
     ```bash
     kubectl get pods -n learning -o wide
     ```
   - Short explanation: Lists the Pod, its IP, node placement, and current state.

3. Delete the imperatively created Pod.
   - What we are doing: Remove the first Pod so the YAML-based version uses the same name cleanly.
   - Command:
     ```bash
     kubectl delete pod nginx-pod -n learning
     ```
   - Short explanation: Deletes the standalone Pod from the namespace.

4. Validate the Pod manifest.
   - What we are doing: Check the reusable manifest before applying it.
   - Command:
     ```bash
     kubectl apply --dry-run=client -f manifests/pods/nginx-pod.yaml
     ```
   - Short explanation: Verifies the YAML is valid from the client side.

5. Create the Pod from YAML.
   - What we are doing: Apply the reusable manifest stored in `manifests/pods/nginx-pod.yaml`.
   - Command:
     ```bash
     kubectl apply -f manifests/pods/nginx-pod.yaml
     ```
   - Short explanation: Creates the nginx Pod declaratively in the `learning` namespace.

6. Read container logs.
   - What we are doing: Confirm the container started cleanly.
   - Command:
     ```bash
     kubectl logs nginx-pod -n learning
     ```
   - Short explanation: Prints the container log stream for the Pod.

7. Open a shell in the container.
   - What we are doing: Inspect the running container from inside.
   - Command:
     ```bash
     kubectl exec -it nginx-pod -n learning -- sh
     ```
   - Short explanation: Starts an interactive shell inside the container.

8. Describe the Pod events.
   - What we are doing: Review scheduling and startup details.
   - Command:
     ```bash
     kubectl describe pod nginx-pod -n learning
     ```
   - Short explanation: Shows labels, image details, status, mounts, and event history.

9. Delete the Pod.
   - What we are doing: Clean up the standalone Pod at the end of the lab.
   - Command:
     ```bash
     kubectl delete -f manifests/pods/nginx-pod.yaml
     ```
   - Short explanation: Deletes the Pod using the same manifest that created it.

## YAML Examples

Main manifest for this lab:

- `manifests/pods/nginx-pod.yaml`

Small example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: learning
```

## Verification

```bash
kubectl get pods -n learning -o wide
kubectl describe pod nginx-pod -n learning
kubectl logs nginx-pod -n learning
kubectl exec nginx-pod -n learning -- hostname
```

## Expected Output

- `kubectl get pods -n learning` should show `nginx-pod` in `Running` state.
- `kubectl logs nginx-pod -n learning` should show the nginx startup log output.
- `kubectl exec nginx-pod -n learning -- hostname` should return the container hostname.
- `kubectl describe pod nginx-pod -n learning` should show scheduling and startup events.

## Troubleshooting

- Pod stays `Pending`:
  Check `kubectl describe pod nginx-pod -n learning` for scheduling issues.
- Image pull errors:
  Look for `ErrImagePull` or `ImagePullBackOff` in the Pod events.
- `kubectl exec` fails:
  Wait until the Pod is running and make sure you use the correct namespace.
- No logs appear:
  Confirm the container has started and the Pod name matches the manifest.

## Cleanup

```bash
kubectl delete -f manifests/pods/nginx-pod.yaml
```

## Key Takeaways

- A Pod is the basic execution unit in Kubernetes.
- Standalone Pods are good for learning but not ideal for managed applications.
- `describe`, `logs`, and `exec` are the fastest first checks when a Pod is not behaving as expected.

## Interview Questions

1. What is a Pod in Kubernetes?
   It is the smallest deployable unit that runs one or more containers.
2. Why are standalone Pods not ideal for most applications?
   They are not self-healing or automatically recreated if deleted.
3. What command helps you inspect Pod events and scheduling details?
   `kubectl describe pod <pod-name> -n <namespace>`.
4. What is the difference between `kubectl logs` and `kubectl exec`?
   `logs` reads container output, while `exec` runs a command inside the container.
