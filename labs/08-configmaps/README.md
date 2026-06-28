# Lab 08 - ConfigMaps

## Objective

Learn how to store non-sensitive configuration in ConfigMaps and consume it as environment variables or mounted files.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- The `learning` namespace from Lab 02
- Basic understanding of Pods

## Key Concepts

- ConfigMaps store non-sensitive configuration data.
- A Pod can consume ConfigMap data as environment variables or files in a mounted volume.
- Environment variables from a ConfigMap do not update inside a running container automatically.
- Mounted ConfigMap files can update, but many applications still need a reload or restart.

## Lab Steps

1. Create a ConfigMap imperatively from literal values.
   - What we are doing: Build a quick ConfigMap directly from the CLI.
   - Command:
     ```bash
     kubectl create configmap app-config --from-literal=APP_MODE=dev --from-literal=LOG_LEVEL=info -n learning
     ```
   - Short explanation: Creates a ConfigMap named `app-config` with two key-value pairs.

2. Delete the imperatively created ConfigMap.
   - What we are doing: Remove it so the reusable YAML version uses the same name cleanly.
   - Command:
     ```bash
     kubectl delete configmap app-config -n learning
     ```
   - Short explanation: Cleans up the quick CLI-created resource before using manifests.

3. Apply the ConfigMap manifest.
   - What we are doing: Create the reusable ConfigMap from YAML.
   - Command:
     ```bash
     kubectl apply -f manifests/configmaps/app-config.yaml
     ```
   - Short explanation: Creates the ConfigMap in the `learning` namespace.

4. Apply the Pod that reads ConfigMap values as environment variables.
   - What we are doing: Test the `envFrom` consumption pattern.
   - Command:
     ```bash
     kubectl apply -f manifests/configmaps/configmap-env-pod.yaml
     ```
   - Short explanation: Creates a Pod that loads ConfigMap keys into container environment variables.

5. Apply the Pod that mounts ConfigMap data as files.
   - What we are doing: Test the volume mount consumption pattern.
   - Command:
     ```bash
     kubectl apply -f manifests/configmaps/configmap-volume-pod.yaml
     ```
   - Short explanation: Creates a Pod that reads ConfigMap keys from mounted files.

6. Verify the environment variable values.
   - What we are doing: Inspect the ConfigMap data from inside the Pod.
   - Command:
     ```bash
     kubectl exec configmap-env-pod -n learning -- printenv | findstr APP_MODE
     ```
   - Short explanation: Confirms the ConfigMap keys were loaded into the container environment.

7. Verify the mounted file values.
   - What we are doing: Read the files created from the ConfigMap volume.
   - Command:
     ```bash
     kubectl exec configmap-volume-pod -n learning -- cat /etc/app-config/APP_MODE
     kubectl exec configmap-volume-pod -n learning -- cat /etc/app-config/LOG_LEVEL
     ```
   - Short explanation: Confirms the ConfigMap keys were mounted as files.

8. Update the ConfigMap.
   - What we are doing: Change the configuration and re-apply it.
   - Command:
     ```bash
     kubectl edit configmap app-config -n learning
     ```
   - Short explanation: Opens the ConfigMap for manual editing so you can change the values.

9. Re-check the Pods after the update.
   - What we are doing: Observe the difference between environment variables and mounted files after a ConfigMap change.
   - Command:
     ```bash
     kubectl exec configmap-env-pod -n learning -- printenv | findstr APP_MODE
     kubectl exec configmap-volume-pod -n learning -- cat /etc/app-config/APP_MODE
     ```
   - Short explanation: Shows that the env-based Pod usually needs recreation, while mounted files may reflect the change.

## YAML Examples

Main manifests for this lab:

- `manifests/configmaps/app-config.yaml`
- `manifests/configmaps/configmap-env-pod.yaml`
- `manifests/configmaps/configmap-volume-pod.yaml`

Small example:

```yaml
data:
  APP_MODE: dev
  LOG_LEVEL: info
```

## Verification

```bash
kubectl get configmaps -n learning
kubectl describe configmap app-config -n learning
kubectl exec configmap-env-pod -n learning -- printenv | findstr LOG_LEVEL
kubectl exec configmap-volume-pod -n learning -- ls /etc/app-config
```

## Expected Output

- `kubectl get configmaps -n learning` should show `app-config`.
- The env-based Pod should print `APP_MODE` and `LOG_LEVEL` values.
- The volume-based Pod should show files such as `/etc/app-config/APP_MODE`.
- After changing the ConfigMap, the mounted files may update before the env-based Pod does.

## Troubleshooting

- Pod cannot find the ConfigMap:
  Confirm the Pod and ConfigMap are in the same namespace.
- Environment variables do not change after editing the ConfigMap:
  Recreate or restart the Pod because env vars are fixed at container start.
- Mounted files are missing:
  Check the volume and volumeMount names in the Pod manifest.
- `kubectl exec` fails:
  Make sure the Pod is running and the namespace is correct.

## Cleanup

```bash
kubectl delete -f manifests/configmaps/configmap-volume-pod.yaml
kubectl delete -f manifests/configmaps/configmap-env-pod.yaml
kubectl delete -f manifests/configmaps/app-config.yaml
```

## Key Takeaways

- ConfigMaps store non-sensitive configuration outside the container image.
- ConfigMap data can be consumed through environment variables or mounted files.
- Update behavior differs between env-based and volume-based consumption.

## Interview Questions

1. When should you use a ConfigMap instead of a Secret?
   Use a ConfigMap for non-sensitive configuration values.
2. How can a Pod consume a ConfigMap?
   It can use environment variables, command arguments, or mounted files.
3. Do environment variables from a ConfigMap update automatically in a running container?
   No, the Pod usually needs to be restarted or recreated.
4. Why are mounted config files sometimes preferred over env vars?
   They can reflect updates without rebuilding the container image.
