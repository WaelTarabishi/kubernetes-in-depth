# Lab 06 - Services

## Objective

Learn how Services provide stable access to Pods whose IP addresses may change. This lab covers selectors, endpoints, and the most common Service types.

## Theory

Pod IPs are ephemeral, so clients should not depend on them directly. A Service provides a stable virtual IP and DNS name for a set of Pods selected by labels. `ClusterIP` is the default internal-only Service type. `NodePort` opens a port on each node, and `LoadBalancer` asks an external load balancer integration to expose the Service. A headless Service skips the virtual IP and returns Pod-level DNS records instead.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- A Deployment or Pods that can serve traffic

## Lab Steps

1. Create a namespace for the lab:
   - `kubectl create namespace services-lab`
2. Create a backend Deployment:
   - `kubectl create deployment web --image=nginx:1.25 -n services-lab`
3. Expose the Deployment with a `ClusterIP` Service:
   - `kubectl expose deployment web --port=80 --target-port=80 --name=web-svc -n services-lab`
4. Inspect the Service and its endpoints:
   - `kubectl get svc,endpoints -n services-lab`
5. Test internal connectivity from a temporary Pod:
   - `kubectl run curl --image=busybox:1.36 --restart=Never -it --rm -n services-lab -- wget -qO- http://web-svc`
6. Scale the backend and confirm the Service still resolves to healthy endpoints:
   - `kubectl scale deployment web --replicas=3 -n services-lab`

## Verification

- `kubectl get svc -n services-lab`
- `kubectl describe svc web-svc -n services-lab`
- `kubectl get endpoints web-svc -n services-lab`
- `kubectl get pods -n services-lab -o wide`

## What I Learned

Expected outcomes after completing this lab:

- I can explain why Services exist even when Pods already have IP addresses.
- I know how label selectors control which Pods receive traffic.
- I can describe when to use `ClusterIP`, `NodePort`, `LoadBalancer`, or headless Services.

## Interview Questions

1. Why should clients avoid connecting directly to Pod IPs?
   - Because Pod IPs are ephemeral and can change when Pods are recreated. Services provide a stable endpoint.

2. What is the default Service type in Kubernetes?
   - `ClusterIP`.

3. What is stored in an Endpoint or EndpointSlice object?
   - The IP addresses and ports of the healthy Pods that back a Service.

4. When would you use a headless Service?
   - When clients need to communicate directly with individual Pods instead of using load balancing (for example, StatefulSets like databases).

5. How does a Service find the Pods it should route to?
   - It uses **label selectors** to match Pods with the appropriate labels.

## Common Mistakes

- Creating a Service selector that does not match any Pods
- Assuming a Service creates application-level health checks automatically
- Confusing Service ports with container ports
- Testing a `NodePort` or `LoadBalancer` workflow on a local cluster without checking platform limitations

## References

- Services, Load Balancing, and Networking: https://kubernetes.io/docs/concepts/services-networking/service/
- DNS for Services and Pods: https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
- EndpointSlices: https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/
