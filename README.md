## Container Security Repository Overview

This repository is designed as a structured reference for container security across a range of platforms and tools.

It begins with the underlying building blocks of containerization—Linux namespaces, cgroups, and capabilities- and builds toward practical security guidance for Docker, Podman, Kubernetes, Rancher, and orchestration or packaging tools like Swarm, Helm, and ArgoCD.

The aim is to provide a practical, accessible resource that supports both learning and real-world application. Whether you’re exploring container fundamentals, securing deployments, or teaching others, this project is meant to serve as a clear and adaptable guide.

---

## Repository Structure
```text
container-security-repo/
├── docs/            # General container security concepts and tooling
├── linux/           # Foundational primitives: namespaces, cgroups, capabilities
├── docker/          # Docker hardening, scanning, runtime security
├── podman/          # Podman-specific security features (e.g., rootless)
├── kubernetes/      # Core K8s security topics (RBAC, PSP, network policies)
├── rancher/         # Rancher-specific security concerns and labs
├── orchestrators/   # Swarm, Helm, ArgoCD, and related tools
├── supply-chain/    # Image signing, SBOMs, CI/CD pipeline security
```

---

## References

[NIST 800-190](https://csrc.nist.gov/pubs/sp/800/190/final) (Application Container Security Guide)

---
