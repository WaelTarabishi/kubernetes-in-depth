# Lab 05 - Deployments

## Objective

Practice managing a stateless application with a Deployment. This lab covers desired state, scaling, rolling updates, rollout history, and rollback.

## Theory

A Deployment is a higher-level controller used for stateless applications. It manages ReplicaSets and updates them declaratively. When a Deployment changes, Kubernetes creates a new ReplicaSet and gradually shifts traffic by scaling replicas up and down according to the rollout strategy. This makes Deployments the standard choice for long-running web applications and APIs.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- Basic understanding of Pods and ReplicaSets
- Access to a test container image such as `nginx`

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace deployments-lab`
2. Create a Deployment:
   - `kubectl create deployment web --image=nginx:1.25 -n deployments-lab`
3. Scale the Deployment:
   - `kubectl scale deployment web --replicas=3 -n deployments-lab`
4. Inspect the Deployment and its ReplicaSets:
   - `kubectl get deployment,rs,pods -n deployments-lab`
5. Update the image and watch the rollout:
   - `kubectl set image deployment/web nginx=nginx:1.27 -n deployments-lab`
   - `kubectl rollout status deployment/web -n deployments-lab`
6. Review rollout history and test rollback:
   - `kubectl rollout history deployment/web -n deployments-lab`
   - `kubectl rollout undo deployment/web -n deployments-lab`

## Verification

- `kubectl get deployments -n deployments-lab`
- `kubectl describe deployment web -n deployments-lab`
- `kubectl rollout status deployment/web -n deployments-lab`
- `kubectl get rs -n deployments-lab`

## What I Learned

Expected outcomes after completing this lab:

- I can explain how Deployments manage ReplicaSets and desired state.
- I can perform a rolling update and observe its progress.
- I know how to inspect rollout history and revert a bad change.

## Interview Questions

1. Why are Deployments preferred for stateless applications?
   - They provide easy scaling, self-healing, and rolling updates without managing Pods manually.

2. What happens internally during a rolling update?
   - The Deployment creates a new ReplicaSet, gradually starts new Pods, and removes old Pods while keeping the application available.

3. How do Deployments and ReplicaSets relate to each other?
   - A Deployment manages ReplicaSets, and each ReplicaSet ensures the desired number of Pods are running.

4. What command shows the status of a rollout?
   - `kubectl rollout status deployment/<deployment-name>`

5. When would you use `kubectl rollout undo`?
   - To roll back a Deployment to the previous working version after a failed or problematic update.

## Common Mistakes

- Updating Pods directly instead of updating the Deployment
- Forgetting to watch rollout status after changing an image
- Assuming a Deployment is appropriate for stateful workloads
- Ignoring old ReplicaSets during troubleshooting

## References

- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- `kubectl rollout`: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#rollout
- Updating Applications: https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/
