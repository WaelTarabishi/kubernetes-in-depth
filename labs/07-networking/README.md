# Lab 07 - Networking

## Objective

Explore Pod-to-Pod communication, Pod-to-Service communication, Service DNS, and basic Kubernetes networking troubleshooting.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- The `learning` namespace from Lab 02
- Basic understanding of Pods and Services

## Key Concepts

- Kubernetes expects a flat network where Pods can reach each other.
- Pod-to-Pod communication uses Pod IP addresses directly.
- Pod-to-Service communication uses a stable Service IP or DNS name.
- CoreDNS provides DNS records for Services and Pods.
- East-west traffic is internal cluster traffic, while north-south traffic enters or leaves the cluster.

## Lab Steps

1. Apply the networking demo workload.
   - What we are doing: Create a backend Deployment and Service for connectivity tests.
   - Command:
     ```bash
     kubectl apply -f manifests/networking/network-test-deployment.yaml
     kubectl apply -f manifests/networking/network-test-service.yaml
     ```
   - Short explanation: Creates a simple nginx backend and a Service for internal traffic testing.

2. Apply the test client Pod.
   - What we are doing: Create a temporary shell Pod for DNS and HTTP checks.
   - Command:
     ```bash
     kubectl apply -f manifests/networking/test-client-pod.yaml
     ```
   - Short explanation: Starts a BusyBox Pod that can run `nslookup`, `wget`, and other basic checks.

3. Verify Pod IP addresses.
   - What we are doing: Observe Pod-to-Pod addressing.
   - Command:
     ```bash
     kubectl get pods -n learning -o wide
     ```
   - Short explanation: Displays Pod IP addresses and node placement.

4. Test Pod-to-Service communication.
   - What we are doing: Access the backend through the Service name.
   - Command:
     ```bash
     kubectl exec -it test-client -n learning -- wget -qO- http://network-test-service
     ```
   - Short explanation: Sends an HTTP request from one Pod to a Service using cluster DNS.

5. Test Service DNS resolution.
   - What we are doing: Resolve the Service name inside the cluster.
   - Command:
     ```bash
     kubectl exec -it test-client -n learning -- nslookup network-test-service
     ```
   - Short explanation: Confirms CoreDNS can resolve the Service name to its ClusterIP.

6. Compare Pod IP access and Service access.
   - What we are doing: Call the backend Pod directly as a learning exercise.
   - Command:
     ```bash
     kubectl get pods -n learning -l app=network-test -o wide
     ```
   - Short explanation: Retrieves the backend Pod IP so you can compare direct Pod access with Service-based access.

7. Inspect DNS and networking components.
   - What we are doing: Check the cluster DNS service when resolution fails.
   - Command:
     ```bash
     kubectl get pods -n kube-system | findstr coredns
     ```
   - Short explanation: Verifies that CoreDNS is running in the cluster.

## YAML Examples

Main manifests for this lab:

- `manifests/networking/network-test-deployment.yaml`
- `manifests/networking/network-test-service.yaml`
- `manifests/networking/test-client-pod.yaml`

Small example:

```yaml
metadata:
  name: network-test-service
  namespace: learning
```

## Verification

```bash
kubectl get pods -n learning -o wide
kubectl get svc,endpoints -n learning
kubectl exec test-client -n learning -- nslookup network-test-service
kubectl exec test-client -n learning -- wget -qO- http://network-test-service
```

## Expected Output

- `kubectl get pods -n learning -o wide` should show distinct Pod IP addresses.
- `nslookup network-test-service` should resolve to the Service ClusterIP.
- `wget -qO- http://network-test-service` should return the nginx default page.
- `kubectl get svc,endpoints -n learning` should show a Service with matching backend endpoints.

## Troubleshooting

- `nslookup` fails:
  Check CoreDNS Pods in `kube-system` and confirm the test Pod is running.
- HTTP request fails:
  Verify the Service selector matches the backend Pod labels.
- Service has no endpoints:
  Check `kubectl get endpoints -n learning`.
- Direct Pod IP works but Service DNS does not:
  Focus on DNS or Service configuration rather than the application container.

## Cleanup

```bash
kubectl delete -f manifests/networking/test-client-pod.yaml
kubectl delete -f manifests/networking/network-test-service.yaml
kubectl delete -f manifests/networking/network-test-deployment.yaml
```

## Key Takeaways

- Pod-to-Pod and Pod-to-Service traffic are both part of normal cluster networking.
- Service DNS reduces the need to track changing Pod IPs.
- CoreDNS is a common first place to investigate when Service names do not resolve.

## Interview Questions

1. What is east-west traffic in Kubernetes?
   It is traffic moving between workloads inside the cluster.
2. What is north-south traffic in Kubernetes?
   It is traffic entering or leaving the cluster from external clients.
3. Why is Service DNS preferred over direct Pod IP access?
   Service names stay stable even when Pods are recreated.
4. What component commonly resolves Service names inside the cluster?
   CoreDNS.
