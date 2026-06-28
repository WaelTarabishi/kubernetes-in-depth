# Lab 09 - Secrets

## Objective

Practice storing and consuming sensitive configuration with Kubernetes Secrets while understanding the security limits of the feature.

## Theory

Secrets are Kubernetes objects intended for sensitive data such as passwords, tokens, or certificates. They can be consumed by workloads in a similar way to ConfigMaps, but they should be protected more carefully through RBAC, namespace boundaries, and encryption at rest where available. Secret values are often represented as base64-encoded strings in manifests, but base64 is encoding, not encryption. A Secret reduces accidental exposure in plain manifests, but it is not a complete secret-management system by itself.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- Basic understanding of ConfigMaps
- Awareness that secret values should not be committed to source control in plain text

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace secrets-lab`
2. Create a Secret from literal values:
   - `kubectl create secret generic app-secret --from-literal=DB_USER=student --from-literal=DB_PASSWORD=changeit -n secrets-lab`
3. Create a Deployment that will consume the Secret:
   - `kubectl create deployment app --image=nginx:1.25 -n secrets-lab`
4. Inject the Secret as environment variables:
   - `kubectl set env deployment/app --from=secret/app-secret -n secrets-lab`
5. Restart the Deployment and verify the variables inside the container:
   - `kubectl rollout restart deployment/app -n secrets-lab`
   - `kubectl exec -n secrets-lab deploy/app -- printenv | findstr DB_USER`
6. Review how the Secret is stored and why RBAC and cluster hardening still matter:
   - `kubectl describe secret app-secret -n secrets-lab`

## Verification

- `kubectl get secrets -n secrets-lab`
- `kubectl describe secret app-secret -n secrets-lab`
- `kubectl exec -n secrets-lab deploy/app -- printenv | findstr DB_PASSWORD`
- `kubectl auth can-i get secrets -n secrets-lab`

## What I Learned

Expected outcomes after completing this lab:

1. When is a Secret more appropriate than a ConfigMap?

   - Use a Secret for sensitive data such as passwords, API keys, tokens, and certificates. Use a ConfigMap for non-sensitive configuration.

2. Does Base64 encoding secure Secret data?

   - No. Base64 is only an encoding format, not encryption. Anyone can decode it if they have access to the Secret.

3. How can you attach Secret data to a workload and verify it?
   - Reference the Secret in a Pod or Deployment (for example, as environment variables or a mounted volume), then verify it using `kubectl exec` to inspect the container's environment or mounted files.

## Interview Questions

1. What is the difference between a Secret and a ConfigMap?
2. Is base64 encoding a security control?
3. How can a Pod consume Secret data?
4. Why is RBAC important when using Secrets?
5. What additional controls improve Secret handling in Kubernetes?

## Common Mistakes

- Committing raw secret values to source control
- Assuming Secrets are fully protected without RBAC and encryption at rest
- Logging secret values to the terminal during verification
- Reusing one Secret across unrelated applications without clear ownership

## References

- Secrets: https://kubernetes.io/docs/concepts/configuration/secret/
- Good Practices for Kubernetes Secrets: https://kubernetes.io/docs/concepts/security/secrets-good-practices/
- Encrypt Data at Rest: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/
