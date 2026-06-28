# Lab 04 - ReplicaSets

## Objective

Learn how a ReplicaSet maintains the desired number of Pod replicas and why label selectors matter.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- The `learning` namespace from Lab 02
- Basic understanding of Pods

## Key Concepts

- A ReplicaSet keeps a specific number of matching Pods running.
- Desired state means Kubernetes keeps reconciling the current state back to the target state.
- Labels and selectors decide which Pods belong to the ReplicaSet.
- Deployments usually manage ReplicaSets in real-world usage because they add rollout and rollback features.

## Lab Steps

1. Review the ReplicaSet manifest.
   - What we are doing: Inspect the YAML before applying it.
   - Command:
     ```bash
     kubectl apply --dry-run=client -f manifests/replicasets/nginx-replicaset.yaml
     ```
   - Short explanation: Checks the manifest structure locally before creating resources.

2. Apply the ReplicaSet manifest.
   - What we are doing: Create a ReplicaSet with three nginx Pods.
   - Command:
     ```bash
     kubectl apply -f manifests/replicasets/nginx-replicaset.yaml
     ```
   - Short explanation: Creates the ReplicaSet and its Pods in the `learning` namespace.

3. Inspect the ReplicaSet and Pods.
   - What we are doing: Verify that the desired replica count is met.
   - Command:
     ```bash
     kubectl get rs,pods -n learning --show-labels
     ```
   - Short explanation: Shows the ReplicaSet, current replicas, and Pod labels.

4. Describe the ReplicaSet.
   - What we are doing: Inspect the selector, template, and events.
   - Command:
     ```bash
     kubectl describe rs nginx-replicaset -n learning
     ```
   - Short explanation: Displays how the ReplicaSet matches Pods and any related events.

5. Delete one Pod and watch it get recreated.
   - What we are doing: Demonstrate self-healing behavior.
   - Command:
     ```bash
     kubectl get pods -n learning -l app=nginx-replicaset
     kubectl delete pod <pod-name> -n learning
     ```
   - Short explanation: Removes one managed Pod so the ReplicaSet creates a replacement automatically.

6. Scale the ReplicaSet.
   - What we are doing: Change the desired replica count from the CLI.
   - Command:
     ```bash
     kubectl scale rs nginx-replicaset --replicas=5 -n learning
     ```
   - Short explanation: Updates the ReplicaSet desired state to five Pods.

7. Scale back to the original size.
   - What we are doing: Return the lab to its starting point.
   - Command:
     ```bash
     kubectl scale rs nginx-replicaset --replicas=3 -n learning
     ```
   - Short explanation: Restores the lab ReplicaSet to three replicas.

## YAML Examples

Main manifest for this lab:

- `manifests/replicasets/nginx-replicaset.yaml`

Small example:

```yaml
selector:
  matchLabels:
    app: nginx-replicaset
```

## Verification

```bash
kubectl get rs -n learning
kubectl get pods -n learning --show-labels
kubectl describe rs nginx-replicaset -n learning
kubectl get events -n learning --sort-by=.metadata.creationTimestamp
```

## Expected Output

- `kubectl get rs -n learning` should show `nginx-replicaset` with `3/3` ready replicas.
- `kubectl get pods -n learning` should show three Pods created from the ReplicaSet template.
- After deleting a Pod, a new Pod with a different name should appear automatically.
- `kubectl describe rs nginx-replicaset -n learning` should show the selector and ReplicaSet events.

## Troubleshooting

- ReplicaSet shows `0` Pods:
  Check that `spec.selector.matchLabels` matches the Pod template labels exactly.
- Pods stay `Pending`:
  Review `kubectl describe pod <pod-name> -n learning` for scheduling or image issues.
- Deleting a Pod removes all workloads:
  Make sure you delete only a Pod, not the ReplicaSet itself.
- Unexpected Pods are managed:
  Review the labels and selector to make sure the ReplicaSet is not matching unrelated Pods.

## Cleanup

```bash
kubectl delete -f manifests/replicasets/nginx-replicaset.yaml
```

## Key Takeaways

- ReplicaSets enforce a desired replica count.
- Label selectors are critical because they define resource ownership.
- Deployments are usually preferred because they manage ReplicaSets and add rollout control.

## Interview Questions

1. What problem does a ReplicaSet solve?
   It keeps a specified number of matching Pods running.
2. How does a ReplicaSet know which Pods belong to it?
   It uses label selectors to match Pods.
3. What happens if a Pod managed by a ReplicaSet is deleted?
   The ReplicaSet creates a replacement Pod to restore the desired count.
4. Why are Deployments usually preferred over standalone ReplicaSets?
   Deployments add rolling updates, rollout history, and rollback support.
