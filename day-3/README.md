# Day 3 - Namespaces & Deployment Strategies

This folder covers Kubernetes Namespaces for resource isolation and Deployment update strategies, specifically RollingUpdate.

## Concepts

### Namespace
A way to logically isolate resources within a cluster. Resources in different namespaces are separated from each other. Useful for organizing environments (dev, staging, prod) or teams within the same cluster.

By default, resources are created in the `default` namespace unless specified otherwise.

### Deployment Update Strategies

Kubernetes supports two update strategies for Deployments:

#### Recreate
Terminates all existing Pods before creating new ones. Causes downtime but ensures no two versions run simultaneously.

#### RollingUpdate (default)
Gradually replaces old Pods with new ones, keeping the application available throughout the update. Controlled by two parameters:

| Parameter | Description |
|-----------|-------------|
| `maxSurge` | Maximum number of Pods that can be created **above** the desired replica count during an update |
| `maxUnavailable` | Maximum number of Pods that can be **unavailable** during an update |

### Resource Requests & Limits

| Field | Description |
|-------|-------------|
| `requests` | Minimum resources guaranteed to the container — used by the scheduler to place the Pod on a node |
| `limits` | Maximum resources the container is allowed to use — enforced at runtime |

## Files

| File | Kind | Description |
|------|------|-------------|
| `namespace.yaml` | `Namespace` | Creates the `giropops` namespace |
| `nginx-deployment.yaml` | `Deployment` | Deploys 3 nginx replicas in the **default** namespace with no explicit update strategy (defaults to RollingUpdate) |
| `nginx-deployment2.yaml` | `Deployment` | Deploys 10 nginx replicas in the **giropops** namespace with an explicit `RollingUpdate` strategy (`maxSurge: 1`, `maxUnavailable: 2`) |

## Apply Order

The namespace must exist before deploying resources into it:

```bash
kubectl apply -f namespace.yaml
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-deployment2.yaml
```

## Verify

```bash
# List namespaces
kubectl get namespaces

# Check deployment in default namespace
kubectl get deployment nginx-deployment

# Check deployment in giropops namespace
kubectl get deployment nginx-deployment2 -n giropops

# Watch a rolling update in progress
kubectl rollout status deployment/nginx-deployment2 -n giropops
```

## Key Notes

- Resources scoped to a namespace must include `namespace: <name>` in their metadata (as seen in `nginx-deployment2.yaml`).
- With `maxSurge: 1` and `maxUnavailable: 2`, during an update Kubernetes can have at most 11 Pods running and at most 2 Pods unavailable at any time, allowing a fast rollout across 10 replicas.
- `requests` affect scheduling decisions; `limits` affect runtime enforcement. Setting both is a best practice.
