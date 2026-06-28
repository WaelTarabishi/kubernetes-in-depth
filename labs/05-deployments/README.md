# Lab 05 - Deployments

## Objective

Learn how Deployments manage application updates, scaling, rollout status, history, and rollback.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- The `learning` namespace from Lab 02
- Basic understanding of Pods and ReplicaSets

## Key Concepts

- A Deployment manages ReplicaSets for stateless workloads.
- Rolling updates replace old Pods gradually instead of deleting everything at once.
- Rollout history helps track Deployment revisions.
- Rollback returns a Deployment to a previous working revision.

## Lab Steps

1. Create a Deployment imperatively.
   - What we are doing: Create a quick nginx Deployment from the CLI.
   - Command:
     ```bash
     kubectl create deployment nginx-deployment --image=nginx:1.25 -n learning
     ```
   - Short explanation: Creates a Deployment with one replica and a generated Pod template.

2. Scale the imperatively created Deployment.
   - What we are doing: Show that Deployments are easy to scale.
   - Command:
     ```bash
     kubectl scale deployment nginx-deployment --replicas=2 -n learning
     ```
   - Short explanation: Updates the Deployment desired replica count.

3. Delete the imperatively created Deployment.
   - What we are doing: Remove it so the YAML-based version can reuse the same resource name cleanly.
   - Command:
     ```bash
     kubectl delete deployment nginx-deployment -n learning
     ```
   - Short explanation: Cleans up the first Deployment before applying the reusable manifest.

4. Validate the declarative manifest.
   - What we are doing: Check the Deployment YAML before applying it.
   - Command:
     ```bash
     kubectl apply --dry-run=client -f manifests/deployments/deployment.yaml
     ```
   - Short explanation: Performs client-side manifest parsing before creation.

5. Apply the Deployment manifest.
   - What we are doing: Create the reusable Deployment from YAML.
   - Command:
     ```bash
     kubectl apply -f manifests/deployments/deployment.yaml
     ```
   - Short explanation: Creates the nginx Deployment in the `learning` namespace.

6. Check rollout status.
   - What we are doing: Confirm the Deployment becomes available.
   - Command:
     ```bash
     kubectl rollout status deployment/nginx-deployment -n learning
     ```
   - Short explanation: Waits for the Deployment to finish rolling out successfully.

7. Review Deployment, ReplicaSet, and Pods.
   - What we are doing: Inspect the resources created under the Deployment.
   - Command:
     ```bash
     kubectl get deployment,rs,pods -n learning
     ```
   - Short explanation: Shows the Deployment and the ReplicaSet it manages.

8. Scale the Deployment.
   - What we are doing: Increase the number of replicas.
   - Command:
     ```bash
     kubectl scale deployment nginx-deployment --replicas=3 -n learning
     ```
   - Short explanation: Updates the Deployment desired state to three Pods.

9. Perform a rolling update.
   - What we are doing: Change the container image version.
   - Command:
     ```bash
     kubectl set image deployment/nginx-deployment nginx=nginx:1.27 -n learning
     ```
   - Short explanation: Updates the container image and triggers a new rollout.

10. Inspect rollout status and history.
   - What we are doing: Watch the update and review revisions.
   - Command:
     ```bash
     kubectl rollout status deployment/nginx-deployment -n learning
     kubectl rollout history deployment/nginx-deployment -n learning
     ```
   - Short explanation: Confirms the rollout succeeded and shows stored revision history.

11. Roll back if needed.
   - What we are doing: Demonstrate how to return to the previous revision.
   - Command:
     ```bash
     kubectl rollout undo deployment/nginx-deployment -n learning
     ```
   - Short explanation: Reverts the Deployment to the previous working revision.

## YAML Examples

Main manifest for this lab:

- `manifests/deployments/deployment.yaml`

Small example:

```yaml
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
```

## Verification

```bash
kubectl get deployments -n learning
kubectl get rs -n learning
kubectl get pods -n learning -o wide
kubectl rollout status deployment/nginx-deployment -n learning
kubectl rollout history deployment/nginx-deployment -n learning
```

## Expected Output

- `kubectl get deployments -n learning` should show `nginx-deployment` as available.
- `kubectl get rs -n learning` should show at least one ReplicaSet owned by the Deployment.
- After updating the image, a new ReplicaSet revision should appear.
- `kubectl rollout history deployment/nginx-deployment -n learning` should show revision entries.

## Troubleshooting

- Rollout stalls:
  Check `kubectl describe deployment nginx-deployment -n learning` and inspect Pod events.
- New Pods fail to start:
  Review the image tag and container events for image pull or startup errors.
- No ReplicaSet appears:
  Confirm the Deployment manifest has a valid selector and Pod template labels.
- Rollback does nothing:
  You need at least one previous successful revision in rollout history.

## Cleanup

```bash
kubectl delete -f manifests/deployments/deployment.yaml
```

## Key Takeaways

- Deployments are the standard controller for stateless applications.
- Rolling updates and rollout history make Deployments safer than standalone Pods or ReplicaSets.
- Rollback is part of normal operational recovery when an update goes wrong.

## Interview Questions

1. What does a Deployment manage internally?
   It manages ReplicaSets and the Pods created by those ReplicaSets.
2. Why are Deployments preferred for stateless applications?
   They provide scaling, rolling updates, rollout history, and rollback.
3. What command checks rollout progress?
   `kubectl rollout status deployment/<deployment-name> -n <namespace>`.
4. What command rolls back the last Deployment change?
   `kubectl rollout undo deployment/<deployment-name> -n <namespace>`.
