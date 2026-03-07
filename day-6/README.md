# Day 6 - Kubernetes Volumes & Storage

This folder covers Kubernetes persistent storage concepts: StorageClass, PersistentVolume (PV), PersistentVolumeClaim (PVC), and how to mount volumes into Pods via a Deployment.

## Concepts

### StorageClass
Defines a "class" of storage with a specific provisioner and reclaim policy. Acts as a template that PVs and PVCs can reference by name.

### PersistentVolume (PV)
A piece of storage provisioned in the cluster. It exists independently of any Pod and has its own lifecycle. Key properties:
- **capacity**: how much storage it offers
- **accessModes**: how it can be mounted (e.g., `ReadWriteOnce` — only one node at a time)
- **reclaimPolicy**: what happens when the PVC is deleted (`Retain` keeps the data)
- **storageClassName**: links it to a StorageClass

### PersistentVolumeClaim (PVC)
A request for storage by a user or workload. Kubernetes binds the PVC to a matching PV based on capacity, access modes, StorageClass, and label selectors.

### Deployment with Volume
A Deployment can reference a PVC to mount persistent storage into its Pods, ensuring data survives Pod restarts.

## Files

| File | Kind | Description |
|------|------|-------------|
| `storageclass.yaml` | `StorageClass` | Creates a StorageClass named `giropops` using `no-provisioner` (manual provisioning), with `WaitForFirstConsumer` binding mode |
| `pv.yaml` | `PersistentVolume` | Creates a 1Gi PV named `meu-pv` using `hostPath` at `/mnt/data`, linked to the `giropops` StorageClass, labeled `storage: lento` |
| `pvc.yaml` | `PersistentVolumeClaim` | Creates a PVC named `meu-pvc` requesting 1Gi from the `giropops` StorageClass, selecting PVs with label `storage: lento` |
| `nginx.yaml` | `Deployment` | Deploys 2 nginx replicas that mount `meu-pvc` at `/var/www` inside the container |

## Apply Order

Resources must be applied in dependency order:

```bash
kubectl apply -f storageclass.yaml
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f nginx.yaml
```

## Verify

```bash
# Check StorageClass
kubectl get storageclass giropops

# Check PV and PVC binding
kubectl get pv meu-pv
kubectl get pvc meu-pvc

# Check Deployment and Pods
kubectl get deployment nginx
kubectl get pods -l app=frontend
```

## Key Notes

- The `WaitForFirstConsumer` binding mode means the PV won't bind to the PVC until a Pod actually requests it.
- `hostPath` is suitable for local/single-node clusters (e.g., kind, minikube) but not for production.
- The `Retain` reclaim policy means the PV data is preserved even after the PVC is deleted — manual cleanup is required.
