# 0004. Use kind for the local Kubernetes cluster

- **Status:** Accepted
- **Date:** 2026-11-22 (recorded 2026-11-24)

## Context

Phases 0, 1 and 3 need a local Kubernetes cluster to learn on, break and rebuild: Kubernetes basics, SPIRE with node and workload attestation, then Istio ambient and agentgateway. The cluster has to:

- Run inside WSL2 on Windows 11 within the 20 GB RAM WSL limit, next to Ollama and VS Code.
- Have **more than one node**, so DaemonSets, scheduling, node failure and per-node agents (SPIRE agent, Istio ztunnel) behave as they would on a real cluster.
- Look like a **kubeadm** cluster (static control-plane pods, kubelet as a service), because that's what the CKA exam uses.
- Be created and destroyed from a file in this repo, in about a minute.

## Decision

Use **kind** (Kubernetes in Docker), running on Docker Desktop with WSL integration. The cluster `lab` is defined in `clusters/lab.yaml`: 1 control-plane node and 2 workers, Kubernetes v1.37.0, default CNI (kindnet).

## Alternatives considered

- **minikube:** good single-node experience and many add-ons. Multi-node is supported but less central to its design, and the add-on model hides components I want to see and break.
- **k3d (k3s in Docker):** fast and light, but k3s replaces etcd with SQLite by default and merges control-plane components into one binary. That's a poor match for learning how a kubeadm cluster is built.
- **Docker Desktop's built-in Kubernetes:** one click, but limited control over version and node layout, and it's not defined in code.
- **kubeadm on VMs (Hyper-V or Vagrant):** closest to the real exam, but much heavier on RAM and slower to rebuild. That's worth doing once for CKA prep (Kubernetes the Hard Way), not as the daily lab.

## Consequences

- Nodes are Docker containers, so a full rebuild is `kind delete cluster --name lab && kind create cluster --config clusters/lab.yaml`.
- Control-plane internals are real kubeadm: static pods in `/etc/kubernetes/manifests`, kubelet under systemd, `crictl` on each node. The experiments in `notes/00-cluster.md` work as they would on a CKA node.
- Reaching Services from Windows needs extra setup: `extraPortMappings` in `clusters/lab.yaml` for NodePorts, or `kubectl port-forward`. `LoadBalancer` Services need `cloud-provider-kind` or MetalLB.
- kindnet is a simple CNI. If Istio ambient or network-policy work in Phase 3 needs a different CNI, that's a new ADR.
- There is no cloud identity (no IMDS, no IRSA or Pod Identity) on kind. Phase 1 federates SPIRE to AWS through OIDC instead, and Phase 2 moves to EKS for the managed version.
