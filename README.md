# Uncomplicating Kubernetes

Study repository based on the [Descomplicando o Kubernetes](https://linuxtips.io/treinamento/descomplicando-o-kubernetes/) course by [LINUXtips](https://linuxtips.io).

Each day folder contains YAML manifests and a README with concepts, file descriptions, and useful commands.

## Course Progress

| Day | Topic | Key Concepts |
|-----|-------|-------------|
| [Day 1](./day-1/) | Local Cluster Setup with kind | kind, control-plane, worker nodes |
| [Day 2](./day-2/) | Pods | Single-container, multi-container, emptyDir, restartPolicy |
| [Day 3](./day-3/) | Namespaces & Deployment Strategies | Namespace, RollingUpdate, Recreate, resource requests/limits |
| [Day 4](./day-4/) | Workload Resources & Probes | ReplicaSet, Deployment, DaemonSet, Liveness/Readiness/Startup probes |
| [Day 6](./day-6/) | Volumes & Storage | StorageClass, PersistentVolume, PersistentVolumeClaim |

## Repository Structure

```
.
├── day-1/          # Local cluster with kind
├── day-2/          # Pods
├── day-3/          # Namespaces and Deployment strategies
├── day-4/          # Workload controllers and health probes
└── day-6/          # Persistent storage
```

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

## Quick Start

```bash
# Create a local cluster (Day 1)
kind create cluster --config day-1/kind.yaml

# Verify the cluster is running
kubectl get nodes
```
