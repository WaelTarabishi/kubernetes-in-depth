# Lab 11 - Ingress

## Objective

Learn how an Ingress routes HTTP traffic to Services and why an Ingress controller is required for the resource to work.

## Prerequisites

- A running Kubernetes cluster
- `kubectl`
- The `learning` namespace from Lab 02
- An ingress controller installed or enabled in the cluster
- Basic understanding of Deployments and Services

## Key Concepts

- An Ingress resource defines HTTP routing rules such as host-based and path-based routing.
- An Ingress controller watches Ingress resources and configures the actual data plane.
- Services expose backends inside the cluster, but they do not provide HTTP host/path routing by themselves.
- Host resolution may require a hosts file entry when testing locally.

## Lab Steps

1. Verify that an ingress controller exists.
   - What we are doing: Confirm the cluster can actually process Ingress resources.
   - Command:
     ```bash
     kubectl get pods -A | findstr ingress
     ```
   - Short explanation: Shows whether an ingress controller is running anywhere in the cluster.

2. Install or enable an ingress controller if needed.
   - What we are doing: Set up a controller for a local learning cluster.
   - Command:
     ```bash
     minikube addons enable ingress
     ```
     or for kind, follow the ingress-nginx installation guide for kind.
   - Short explanation: Ingress resources do nothing by themselves until a controller is installed.

3. Apply the backend Deployment.
   - What we are doing: Create the nginx workload that will receive HTTP traffic.
   - Command:
     ```bash
     kubectl apply -f manifests/ingress/ingress-deployment.yaml
     ```
   - Short explanation: Creates the backend Pods in the `learning` namespace.

4. Apply the backend Service.
   - What we are doing: Expose the Deployment inside the cluster.
   - Command:
     ```bash
     kubectl apply -f manifests/ingress/ingress-service.yaml
     ```
   - Short explanation: Creates a Service that the Ingress will forward traffic to.

5. Apply the Ingress resource.
   - What we are doing: Create host-based routing for `k8s.local`.
   - Command:
     ```bash
     kubectl apply -f manifests/ingress/ingress.yaml
     ```
   - Short explanation: Creates an Ingress rule that routes `/` on `k8s.local` to the backend Service.

6. Inspect the Ingress.
   - What we are doing: Check the controller-assigned address and rule details.
   - Command:
     ```bash
     kubectl get ingress -n learning
     kubectl describe ingress nginx-ingress -n learning
     ```
   - Short explanation: Shows the host, path, backend Service, and controller status information.

7. Add local host resolution if required.
   - What we are doing: Make `k8s.local` resolve to the ingress endpoint during local testing.
   - Command:
     ```text
     Add an entry such as 127.0.0.1 k8s.local to your hosts file if your local setup needs it.
     ```
   - Short explanation: Local clusters often need a manual hosts file entry for custom hostnames.

8. Test the route with `curl`.
   - What we are doing: Send an HTTP request using the expected `Host` header.
   - Command:
     ```bash
     curl -H "Host: k8s.local" http://127.0.0.1/
     ```
   - Short explanation: Verifies that the Ingress routes traffic to the backend Service.

9. Review path-based routing.
   - What we are doing: Understand how the same Ingress could route different URL paths to different Services.
   - Command:
     ```bash
     kubectl get ingress nginx-ingress -n learning -o yaml
     ```
   - Short explanation: Shows the `paths` section that can be extended for multiple backends.

## YAML Examples

Main manifests for this lab:

- `manifests/ingress/ingress-deployment.yaml`
- `manifests/ingress/ingress-service.yaml`
- `manifests/ingress/ingress.yaml`

Small example:

```yaml
rules:
  - host: k8s.local
```

## Verification

```bash
kubectl get deploy,svc,ingress -n learning
kubectl describe ingress nginx-ingress -n learning
curl -H "Host: k8s.local" http://127.0.0.1/
```

## Expected Output

- `kubectl get ingress -n learning` should show an Ingress rule for `k8s.local`.
- `kubectl describe ingress nginx-ingress -n learning` should show the backend Service and path `/`.
- `curl -H "Host: k8s.local" http://127.0.0.1/` should return the nginx default page when the controller is reachable.
- The Deployment and Service should both be available in the `learning` namespace.

## Troubleshooting

- No ingress controller:
  Install or enable one first, otherwise the Ingress resource will not route traffic.
- Wrong service name:
  Make sure the backend Service name in the Ingress matches `nginx-ingress-service`.
- Wrong service port:
  Confirm the Ingress backend port matches the Service port.
- Host does not resolve:
  Add a hosts file entry or use the controller address directly with the correct `Host` header.
- Backend Pods not ready:
  Check `kubectl get pods -n learning` and `kubectl describe deployment nginx-ingress-app -n learning`.

## Cleanup

```bash
kubectl delete -f manifests/ingress/ingress.yaml
kubectl delete -f manifests/ingress/ingress-service.yaml
kubectl delete -f manifests/ingress/ingress-deployment.yaml
```

## Key Takeaways

- Ingress adds HTTP routing rules on top of Services.
- An Ingress controller is required for Ingress resources to work.
- Host-based and path-based routing are common reasons to use Ingress instead of Services alone.

## Interview Questions

1. What problem does Ingress solve?
   It routes HTTP and HTTPS traffic to Services based on rules such as hostnames and paths.
2. Why is an ingress controller required?
   The controller reads the Ingress resource and configures the actual traffic-handling component.
3. How is Ingress different from a Service of type LoadBalancer?
   A LoadBalancer exposes one Service, while Ingress can route HTTP traffic to multiple Services using rules.
4. What is path-based routing?
   It sends different URL paths to different backend Services.
5. What should you check first if Ingress is not routing traffic?
   Check that an ingress controller is installed and that the backend Service name and port are correct.
