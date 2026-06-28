# Lab 10 - Volumes

## Objective

Understand why container filesystem data is not durable and practice using `emptyDir` and `PersistentVolumeClaim` storage patterns.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- The `learning` namespace from Lab 02
- A default `StorageClass` if you want the PVC example to bind dynamically

## Key Concepts

- Container writable layers are usually ephemeral and should not be treated as durable storage.
- `emptyDir` exists for the lifetime of a Pod and is deleted when the Pod is removed.
- A `PersistentVolumeClaim` requests persistent storage for a Pod.
- A `PersistentVolume` is cluster-scoped, while a PVC is namespaced.
- A `StorageClass` defines how dynamic storage can be provisioned.
- Access modes and reclaim policies affect how storage is used and cleaned up.

## Lab Steps

1. Apply the `emptyDir` example.
   - What we are doing: Create a Pod with temporary shared storage.
   - Command:
     ```bash
     kubectl apply -f manifests/volumes/emptydir-pod.yaml
     ```
   - Short explanation: Creates a Pod that mounts an `emptyDir` volume at `/data`.

2. Write data into the `emptyDir` volume.
   - What we are doing: Store a test file inside the Pod volume.
   - Command:
     ```bash
     kubectl exec emptydir-pod -n learning -- sh -c "echo hello-from-emptydir > /data/message.txt"
     ```
   - Short explanation: Writes a file to the Pod-backed temporary volume.

3. Verify the `emptyDir` file exists.
   - What we are doing: Confirm the file was written.
   - Command:
     ```bash
     kubectl exec emptydir-pod -n learning -- cat /data/message.txt
     ```
   - Short explanation: Reads the file from inside the Pod.

4. Delete and recreate the `emptyDir` Pod.
   - What we are doing: Show that the data is lost when the Pod is removed.
   - Command:
     ```bash
     kubectl delete -f manifests/volumes/emptydir-pod.yaml
     kubectl apply -f manifests/volumes/emptydir-pod.yaml
     ```
   - Short explanation: Recreates the Pod and resets the temporary `emptyDir` storage.

5. Check available storage classes.
   - What we are doing: See whether the cluster can dynamically provision storage.
   - Command:
     ```bash
     kubectl get storageclass
     ```
   - Short explanation: Lists storage classes and usually shows which one is the default.

6. Apply the PVC manifest.
   - What we are doing: Request persistent storage.
   - Command:
     ```bash
     kubectl apply -f manifests/volumes/pvc.yaml
     ```
   - Short explanation: Creates a PVC in the `learning` namespace.

7. Verify the PVC status.
   - What we are doing: Confirm whether the claim is bound.
   - Command:
     ```bash
     kubectl get pvc -n learning
     ```
   - Short explanation: Shows whether the PVC is `Pending` or `Bound`.

8. Apply the Pod that mounts the PVC.
   - What we are doing: Attach persistent storage to a Pod.
   - Command:
     ```bash
     kubectl apply -f manifests/volumes/pvc-pod.yaml
     ```
   - Short explanation: Creates a Pod that mounts the claim at `/data`.

9. Write data to the PVC-backed volume.
   - What we are doing: Store data that should survive Pod recreation.
   - Command:
     ```bash
     kubectl exec pvc-pod -n learning -- sh -c "echo hello-from-pvc > /data/persistent-message.txt"
     ```
   - Short explanation: Writes a file into the persistent volume mounted by the Pod.

10. Recreate the Pod and verify the PVC data remains.
   - What we are doing: Prove that persistent storage survives Pod replacement.
   - Command:
     ```bash
     kubectl delete -f manifests/volumes/pvc-pod.yaml
     kubectl apply -f manifests/volumes/pvc-pod.yaml
     kubectl exec pvc-pod -n learning -- cat /data/persistent-message.txt
     ```
   - Short explanation: Recreates the Pod and checks whether the file is still present on the mounted claim.

## YAML Examples

Main manifests for this lab:

- `manifests/volumes/emptydir-pod.yaml`
- `manifests/volumes/pvc.yaml`
- `manifests/volumes/pvc-pod.yaml`

Small example:

```yaml
volumes:
  - name: app-storage
    persistentVolumeClaim:
      claimName: app-pvc
```

## Verification

```bash
kubectl get storageclass
kubectl get pvc -n learning
kubectl get pv
kubectl exec emptydir-pod -n learning -- ls /data
kubectl exec pvc-pod -n learning -- ls /data
```

## Expected Output

- The `emptyDir` file should disappear after the Pod is deleted and recreated.
- The PVC should move to `Bound` if the cluster can provision storage.
- The file written through the PVC-backed Pod should still exist after the Pod is recreated.
- `kubectl get pv` should show the persistent volume backing the claim when one is provisioned.

## Troubleshooting

- PVC stays `Pending`:
  The cluster may not have a matching `StorageClass`, dynamic provisioner, or compatible access mode.
- Pod stays `Pending` after mounting a PVC:
  Check whether the claim is still unbound or references a missing storage class.
- Data disappears from `emptyDir`:
  That is expected after Pod deletion because `emptyDir` is Pod-scoped.
- No default `StorageClass` exists:
  Use a cluster that supports dynamic provisioning or create storage manually before retrying.

## Cleanup

```bash
kubectl delete -f manifests/volumes/pvc-pod.yaml
kubectl delete -f manifests/volumes/pvc.yaml
kubectl delete -f manifests/volumes/emptydir-pod.yaml
```

## Key Takeaways

- Container-local writes are usually not durable enough for important data.
- `emptyDir` is temporary Pod storage.
- PVCs are the standard way for Pods to request persistent storage.
- `StorageClass` controls how storage is provisioned.

## Interview Questions

1. Why is container filesystem data usually not considered durable?
   Because it is tied to the container lifecycle and can disappear when the container or Pod is replaced.
2. What is the difference between emptyDir and a PersistentVolumeClaim?
   `emptyDir` is temporary Pod storage, while a PVC requests persistent storage that can outlive a Pod.
3. What does a StorageClass do?
   It defines how storage should be provisioned and what properties it should have.
4. When is a PersistentVolume cluster-scoped?
   A PersistentVolume is always cluster-scoped.
5. Why might a Pod remain pending when using a PVC?
   The claim may be unbound because of missing storage, wrong access mode, or storage class issues.
