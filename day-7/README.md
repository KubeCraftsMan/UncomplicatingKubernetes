# Day 7 - Kubernetes Services & StatefulSets

This folder covers Kubernetes **Services** (ClusterIP and Headless) and **StatefulSets**, using nginx as an example workload.

## Files

### `nginx-deployment.yaml`
A `Deployment` running 3 replicas of nginx (port 80), labeled `app: nginx` and `env: dev`.

### `nginx-clusterip-svc.yaml`
A `ClusterIP` Service that exposes the nginx Deployment internally within the cluster on port 80.

### `nginx-headless-svc.yaml`
A **Headless** Service (`clusterIP: None`) for nginx. Instead of load-balancing, it returns the individual Pod IPs via DNS — required for StatefulSets.

### `statefulset-nginx.yaml`
A `StatefulSet` running 3 nginx replicas with:
- Stable network identities (pods named `nginx-0`, `nginx-1`, `nginx-2`)
- Persistent storage via `volumeClaimTemplates` — each pod gets a 1Gi `ReadWriteOnce` PVC mounted at `/usr/share/nginx/html`

## Usage

Apply all manifests:

```bash
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-clusterip-svc.yaml
kubectl apply -f nginx-headless-svc.yaml
kubectl apply -f statefulset-nginx.yaml
```

Check resources:

```bash
kubectl get deployments,statefulsets,services,pods
```

## Key Concepts

| Concept | Description |
|---|---|
| **ClusterIP Service** | Exposes pods internally via a stable virtual IP with load balancing |
| **Headless Service** | No virtual IP; DNS resolves directly to pod IPs. Required by StatefulSets |
| **StatefulSet** | Manages stateful apps — guarantees stable pod names, ordered deployment, and persistent volumes |
