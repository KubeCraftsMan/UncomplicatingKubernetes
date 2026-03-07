# Day 4 - Kubernetes Workload Resources & Probes

This folder covers core Kubernetes workload controllers (ReplicaSet, Deployment, DaemonSet) and the three types of health probes (Liveness, Readiness, Startup).

## Concepts

### ReplicaSet
Ensures a specified number of Pod replicas are running at all times. If a Pod dies, the ReplicaSet creates a new one. In practice, ReplicaSets are rarely managed directly — Deployments manage them for you.

### Deployment
Wraps a ReplicaSet and adds rolling update and rollback capabilities. It is the standard way to run stateless applications in Kubernetes. Supports resource requests/limits per container.

### DaemonSet
Ensures that **every node** in the cluster runs exactly one copy of a Pod. Useful for node-level agents like log collectors, monitoring exporters (e.g., `node-exporter`), or network plugins.

### Health Probes
Kubernetes uses probes to know whether a container is alive and ready to serve traffic. All probes share common configuration fields:

| Field | Description |
|-------|-------------|
| `initialDelaySeconds` | How long to wait after the container starts before the first probe |
| `periodSeconds` | How often to run the probe |
| `timeoutSeconds` | How long to wait for a probe response |
| `failureThreshold` | Consecutive failures before taking action |
| `successThreshold` | Consecutive successes to consider the probe passing |

#### Liveness Probe
Checks if the container is still **alive**. If it fails, Kubernetes restarts the container. Used to recover from deadlocks or hung processes.

#### Readiness Probe
Checks if the container is **ready to receive traffic**. If it fails, the Pod is removed from the Service's endpoints (no traffic is sent to it) but the container is NOT restarted.

#### Startup Probe
Checks if the application has **finished starting up**. While the startup probe is running, liveness and readiness probes are disabled. Useful for slow-starting applications — set a high `failureThreshold` to give the app enough time to boot.

## Files

| File | Kind | Description |
|------|------|-------------|
| `nginx-replicaset.yaml` | `ReplicaSet` | Runs 3 nginx:1.19.0 replicas with CPU/memory limits |
| `nginx-deployment.yaml` | `Deployment` | Deploys 3 nginx:1.19.0 replicas with resource requests and limits |
| `nginx-daemonset.yaml` | `DaemonSet` | Runs `node-exporter` on every node, mounting `/proc` and `/sys` from the host for metrics collection |
| `nginx-livessprobe.yaml` | `Deployment` | nginx with a **Liveness Probe** using HTTP GET on port 80 |
| `nginx-readnessprobe.yaml` | `Deployment` | nginx with a **Readiness Probe** using `curl` exec command on port 80 |
| `nginx-startupprobe.yaml` | `Deployment` | nginx with a **Startup Probe** using HTTP GET, allowing up to 12 failures (120s) for startup |

## Probe Comparison

| Probe | Method | Action on failure | Use case |
|-------|--------|-------------------|----------|
| Liveness | HTTP GET `/ :80` | Restart container | Detect hung/deadlocked containers |
| Readiness | `exec curl -f http://localhost:80/` | Remove from Service endpoints | Wait until app is ready to serve traffic |
| Startup | HTTP GET `/ :80` | Restart container (after threshold) | Give slow apps time to initialize |

## Apply Examples

```bash
# Apply all resources
kubectl apply -f nginx-replicaset.yaml
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-daemonset.yaml
kubectl apply -f nginx-livessprobe.yaml
kubectl apply -f nginx-readnessprobe.yaml
kubectl apply -f nginx-startupprobe.yaml
```

## Verify

```bash
# Check ReplicaSet
kubectl get replicaset nginx-replicaset

# Check Deployments
kubectl get deployments

# Check DaemonSet (runs on all nodes)
kubectl get daemonset node-exporter-daemonset

# Describe a pod to inspect probe events
kubectl describe pod <pod-name>
```

## Key Notes

- **ReplicaSet vs Deployment**: prefer Deployments in practice — they manage ReplicaSets and support rolling updates.
- **DaemonSet + hostNetwork**: the `node-exporter` DaemonSet uses the node's network stack directly (`hostNetwork: true`) so metrics are exposed on the node's IP at port `9100`.
- **Readiness vs Liveness**: a failing readiness probe stops traffic to the Pod; a failing liveness probe kills and restarts it. They serve different purposes and can coexist.
- **Startup probe + liveness**: combining a startup probe with a liveness probe is a best practice for slow-starting apps — the startup probe buys time, then the liveness probe takes over.
