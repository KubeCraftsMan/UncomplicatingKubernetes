# Day 1 - Local Cluster Setup with kind

This folder covers setting up a local Kubernetes cluster using **kind** (Kubernetes IN Docker).

## Concepts

### kind (Kubernetes IN Docker)
kind is a tool for running local Kubernetes clusters using Docker containers as nodes. It is mainly used for local development and learning. Each node in the cluster is a Docker container running on your machine.

### Cluster Roles

| Role | Description |
|------|-------------|
| `control-plane` | Manages the cluster state, schedules workloads, and exposes the Kubernetes API |
| `worker` | Runs application workloads (Pods) assigned by the control-plane |

## Files

| File | Description |
|------|-------------|
| `kind.yaml` | Defines a local cluster with 1 control-plane node and 2 worker nodes |

## Creating the Cluster

```bash
# Create the cluster using the config file
kind create cluster --config kind.yaml

# Verify the cluster nodes are running
kubectl get nodes
```

## Useful Commands

```bash
# List all kind clusters
kind get clusters

# Delete the cluster
kind delete cluster

# Check cluster info
kubectl cluster-info
```

## Key Notes

- kind requires Docker to be running on your machine.
- The `control-plane` node also runs system components like `kube-apiserver`, `etcd`, `kube-scheduler`, and `kube-controller-manager`.
- Worker nodes run the `kubelet` and `kube-proxy` components to manage and route traffic to Pods.
- kind clusters are purely local — they are not suitable for production use.
