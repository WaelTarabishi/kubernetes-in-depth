# Lab 07 - Networking

## Objective

Explore the Kubernetes networking model by testing Pod-to-Pod, Pod-to-Service, and DNS-based communication inside the cluster.

## Theory

Kubernetes assumes a flat cluster network where Pods can reach each other without NAT. A Container Network Interface (CNI) plugin implements this model. Service discovery is usually backed by CoreDNS, which gives Services stable DNS names. Traffic to a Service is translated to backend Pods by kube-proxy or a replacement data plane, depending on the cluster implementation.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- Basic understanding of Pods and Services
- Two simple workloads or one workload plus a temporary debug Pod

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace networking-lab`
2. Create a backend Deployment and Service:
   - `kubectl create deployment web --image=nginx:1.25 -n networking-lab`
   - `kubectl expose deployment web --port=80 --target-port=80 --name=web-svc -n networking-lab`
3. Launch a temporary debug Pod:
   - `kubectl run toolbox --image=busybox:1.36 --restart=Never -it --rm -n networking-lab -- sh`
4. From the debug Pod, test DNS and Service access:
   - `nslookup web-svc`
   - `wget -qO- http://web-svc`
5. Inspect Pod IPs and test direct Pod-to-Pod communication if needed:
   - `kubectl get pods -o wide -n networking-lab`
6. Record how DNS, Service abstraction, and Pod IPs differ during the tests.

## Verification

- `kubectl get pods -o wide -n networking-lab`
- `kubectl get svc,endpoints -n networking-lab`
- `kubectl exec -n networking-lab deploy/web -- nslookup web-svc`
- `kubectl get pods -n kube-system | findstr coredns`

## What I Learned

Expected outcomes after completing this lab:

- I can describe the flat networking model Kubernetes expects.
- I understand how DNS and Services simplify communication between workloads.
- I know where to start when basic cluster connectivity fails.

## Interview Questions

1. What networking assumptions does Kubernetes make about Pods?
   - Kubernetes assumes a flat network where every Pod can directly communicate with every other Pod using its IP address without NAT.

2. What role does a CNI plugin play?
   - A CNI plugin provides Pod networking by assigning Pod IP addresses and enabling communication between Pods across the cluster.

3. How does a Pod resolve a Service name such as `web-svc`?
   - The Pod sends a DNS query to CoreDNS, which resolves the Service name (`web-svc`) to its ClusterIP.

4. What is the purpose of kube-proxy?
   - kube-proxy watches Services and EndpointSlices, then configures the node's networking to forward traffic from a Service's ClusterIP to one of its backend Pods.

5. Why is Service-based access usually preferred over direct Pod IP access?
   - Because Service IPs and DNS names are stable, while Pod IPs are ephemeral and change when Pods are recreated.

## Common Mistakes

- Treating Pod IPs as permanent addresses
- Forgetting that DNS failures may come from CoreDNS or network policy issues
- Testing connectivity without checking whether the Service has endpoints
- Assuming every cluster uses the same network plugin or traffic path

## References

- The Kubernetes Network Model: https://kubernetes.io/docs/concepts/services-networking/
- DNS for Services and Pods: https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
- Network Plugins: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/
