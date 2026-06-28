# Lab 08 - ConfigMaps

## Objective

Learn how to store non-sensitive application configuration outside a container image by using ConfigMaps.

## Theory

A ConfigMap stores key-value configuration data that is not secret. Workloads can consume ConfigMaps as environment variables, command-line arguments, or mounted files. This keeps images reusable across environments because configuration changes do not require rebuilding the image. A common operational detail is that environment variables are fixed when the container starts, while mounted ConfigMap files can update later but applications may still need a reload or restart to use the new values.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- A simple workload to attach configuration to
- Basic understanding of Pods or Deployments

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace configmaps-lab`
2. Create a ConfigMap from literal values:
   - `kubectl create configmap app-config --from-literal=APP_MODE=dev --from-literal=LOG_LEVEL=info -n configmaps-lab`
3. Create a Deployment that will consume the configuration:
   - `kubectl create deployment app --image=nginx:1.25 -n configmaps-lab`
4. Inject the ConfigMap as environment variables:
   - `kubectl set env deployment/app --from=configmap/app-config -n configmaps-lab`
5. Restart the workload and inspect the container environment:
   - `kubectl rollout restart deployment/app -n configmaps-lab`
   - `kubectl exec -n configmaps-lab deploy/app -- printenv | findstr APP_MODE`
6. Update the ConfigMap and repeat the verification so the update behavior is clear:
   - `kubectl create configmap app-config --from-literal=APP_MODE=test --from-literal=LOG_LEVEL=debug -n configmaps-lab -o yaml --dry-run=client | kubectl apply -f -`

## Verification

- `kubectl get configmaps -n configmaps-lab`
- `kubectl describe configmap app-config -n configmaps-lab`
- `kubectl exec -n configmaps-lab deploy/app -- printenv | findstr LOG_LEVEL`
- `kubectl rollout status deployment/app -n configmaps-lab`

## What I Learned

Expected outcomes after completing this lab:

- I can separate non-sensitive configuration from the container image.
- I understand common ways a workload can consume a ConfigMap.
- I know why configuration updates often require a restart or application reload strategy.

## Interview Questions

1. When should you use a ConfigMap instead of a Secret?

   - Use a ConfigMap for non-sensitive configuration (for example, application settings). Use a Secret for sensitive data such as passwords, API keys, and certificates.

2. How can a Pod consume data from a ConfigMap?

   - A Pod can consume a ConfigMap as:
     - Environment variables
     - Mounted files in a volume
     - Command-line arguments

3. Do environment variables sourced from a ConfigMap update automatically in a running container?

   - No. The Pod must be restarted to pick up the updated environment variables.

4. Why is externalized configuration useful for image reuse?

   - It allows the same container image to be used in different environments (development, staging, production) by changing only the configuration instead of rebuilding the image.

5. What are the operational tradeoffs between environment variables and mounted config files?
   - Environment variables are simple but require a Pod restart when the ConfigMap changes. Mounted config files can reflect ConfigMap updates without rebuilding the image, although applications may still need to reload the changed files.

## Common Mistakes

- Storing sensitive values in a ConfigMap
- Assuming updated environment variables appear in already running containers
- Coupling configuration too tightly to one image build
- Forgetting to verify that the keys expected by the application actually exist

## References

- ConfigMaps: https://kubernetes.io/docs/concepts/configuration/configmap/
- Configure a Pod to Use a ConfigMap: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/
- Configuration Best Practices: https://kubernetes.io/docs/concepts/configuration/overview/
