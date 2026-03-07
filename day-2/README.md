# Day 2 - Pods

This folder covers the most fundamental unit in Kubernetes: the **Pod**. Topics include single-container Pods, multi-container Pods, and ephemeral volumes with `emptyDir`.

## Concepts

### Pod
The smallest deployable unit in Kubernetes. A Pod wraps one or more containers that share the same network namespace (same IP, same ports) and can share storage volumes. Pods are ephemeral — if they die, they are not automatically restarted unless managed by a controller (e.g., Deployment).

### Multi-Container Pod
Multiple containers can run inside the same Pod. They communicate via `localhost` and can share volumes. Common patterns include sidecar (helper container alongside the main app) and ambassador containers.

### emptyDir Volume
A temporary volume created when a Pod is assigned to a node and deleted when the Pod is removed. All containers in the Pod can read and write to it, making it useful for sharing data between containers within the same Pod. It does **not** persist across Pod restarts.

### restartPolicy
Defines what Kubernetes does when a container exits:

| Value | Behavior |
|-------|----------|
| `Always` | Always restart the container (default) |
| `OnFailure` | Restart only on non-zero exit codes |
| `Never` | Never restart |

### dnsPolicy
Controls how DNS resolution works for the Pod. `ClusterFirst` (the default) means the Pod uses the cluster's internal DNS first, falling back to the node's DNS.

## Files

| File | Kind | Description |
|------|------|-------------|
| `pod.yaml` | `Pod` | A simple single-container nginx Pod with resource requests and limits |
| `pod-multi-container.yaml` | `Pod` | A Pod with two containers: nginx and an ubuntu container running a keep-alive loop |
| `pod-empty-dir.yaml` | `Pod` | An nginx Pod with an `emptyDir` volume mounted at `/usr/share/nginx/html`, limited to 256Mi |

## Apply Examples

```bash
kubectl apply -f pod.yaml
kubectl apply -f pod-multi-container.yaml
kubectl apply -f pod-empty-dir.yaml
```

## Verify

```bash
# List all pods
kubectl get pods

# Describe a pod (events, volumes, container status)
kubectl describe pod nginx
kubectl describe pod multicontainer

# Access a specific container in a multi-container pod
kubectl exec -it multicontainer -c nginx -- bash
kubectl exec -it multicontainer -c ubuntu -- bash

# Check logs from a specific container
kubectl logs multicontainer -c nginx
```

## Key Notes

- All containers in a Pod share the same IP address — they communicate via `localhost`.
- The ubuntu container in `pod-multi-container.yaml` runs `while true; do sleep 30; done` to stay alive, since a container that exits immediately would be restarted by the `Always` restart policy.
- `emptyDir` is useful for inter-container file sharing within a Pod, but data is lost when the Pod is deleted or rescheduled.
- Resource `requests` are used by the scheduler to find a suitable node; `limits` cap the container's actual usage at runtime.
