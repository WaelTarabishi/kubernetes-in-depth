# Lab 10 - Volumes

## Objective

Understand how Kubernetes volumes handle data that should outlive a container process and how persistent storage differs from ephemeral storage.

## Theory

Container filesystems are usually ephemeral. If a container is replaced, local writes inside the container image layer are lost. Kubernetes volumes provide storage that can be shared by containers in a Pod or retained beyond container restarts. `emptyDir` is created when a Pod starts and removed when the Pod is deleted. Persistent storage is usually requested through a `PersistentVolumeClaim` and backed by a `StorageClass` that provisions or connects real storage.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- Basic understanding of Pods
- A cluster with a default `StorageClass` if you want to test dynamic persistent storage

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace volumes-lab`
2. Start with an `emptyDir` example to observe ephemeral behavior:
   - Create a Pod that mounts `emptyDir` at `/data`
   - Write a test file to `/data`
   - Delete the Pod and confirm the file is gone after recreation
3. Check whether the cluster has a default storage class:
   - `kubectl get storageclass`
4. If a storage class exists, create a `PersistentVolumeClaim` and mount it into a Pod or Deployment.
5. Write a file to the persistent mount, restart the Pod, and verify that the file still exists.
6. Compare the outcomes of `emptyDir` and persistent storage in your notes.

## Verification

- `kubectl get pv`
- `kubectl get pvc -n volumes-lab`
- `kubectl get storageclass`
- `kubectl exec -it <pod-name> -n volumes-lab -- ls /data`

## What I Learned

Expected outcomes after completing this lab:

- I can explain why container-local filesystem state is usually not enough.
- I understand the difference between ephemeral volumes and persistent storage.
- I know the roles of `PersistentVolume`, `PersistentVolumeClaim`, and `StorageClass`.

## Interview Questions

1. Why is container filesystem data usually not considered durable?
   - Data written inside a container's normal writable filesystem is tied to that container's lifecycle. If the container crashes, is stopped, recreated, or rescheduled, Kubernetes can start it again with a clean filesystem state, so files created during the previous run can be lost. Kubernetes documentation describes on-disk files in a container as ephemeral and recommends volumes when data must survive restarts or replacement.

2. What is the difference between `emptyDir` and a `PersistentVolumeClaim`?
   - `emptyDir` is temporary Pod-level storage. It is created when the Pod is assigned to a node, can be shared by containers in that same Pod, survives container crashes, but is deleted permanently when the Pod is removed from the node.
   - A `PersistentVolumeClaim` is a request for persistent storage. The PVC asks Kubernetes for storage with a certain size, access mode, and optionally a `StorageClass`; Kubernetes then binds it to a `PersistentVolume`. A PV has a lifecycle independent of any individual Pod.

3. What does a `StorageClass` do?
   - A `StorageClass` defines a type or "class" of storage that the cluster offers, such as fast SSD, standard disk, encrypted storage, or backup-enabled storage. It tells Kubernetes which provisioner to use and what parameters or reclaim policy to apply when dynamically creating a `PersistentVolume` for a PVC.

4. When is a `PersistentVolume` cluster-scoped?
   - A `PersistentVolume` is always cluster-scoped in Kubernetes. It is a cluster resource, similar to a node, and is not created inside a namespace. The `PersistentVolumeClaim` is the namespaced object; the PV itself belongs to the cluster and can be bound to a PVC.

5. Why might a Pod remain pending when using a PVC?
   - A Pod can remain `Pending` if the PVC cannot be bound to usable storage. Common causes include no matching PV, requested size too large, wrong access mode, wrong or missing `StorageClass`, dynamic provisioner failure, or topology and zone mismatch. Kubernetes says PVCs remain unbound indefinitely if no matching volume exists, and Pods use claims only after Kubernetes finds the bound volume.
   - Another common cause is `StorageClass` `volumeBindingMode`: with `Immediate`, storage may be provisioned without considering where the Pod can run, which can make the Pod unschedulable; with `WaitForFirstConsumer`, binding waits until a Pod uses the PVC so topology can be considered.

## Common Mistakes

- Writing important data only to the container filesystem
- Assuming a local learning cluster always has a dynamic provisioner installed
- Ignoring access modes and storage class requirements
- Expecting `emptyDir` data to survive Pod deletion

## References

- Volumes: https://kubernetes.io/docs/concepts/storage/volumes/
- Persistent Volumes: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
- Storage Classes: https://kubernetes.io/docs/concepts/storage/storage-classes/
