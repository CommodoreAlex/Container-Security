# Kubernetes Security

## Overview
Covers **Kubernetes (K8s) security**, including RBAC, pod security, network policies, supply chain, and runtime defenses.  

## Contents
- **usage.md** – Minimal K8s CLI introduction (kubectl) and deployment concepts.
- **basics.md** – Core Kubernetes concepts relevant for security.
- **rbac.md** – Role-based access control and privilege management.
- **network-policies.md** – Network segmentation best practices.
- **pod-security.md** – Pod Security Admission / PSP policies.
- **supply-chain.md** – Secure image use, admission control, CI/CD integration.
- **runtime-security.md** – Runtime monitoring, OPA/Gatekeeper, Falco rules.
- **Manifests/** – Example deployments, RBAC policies, network policies.

## Cross-References
- Linux primitives (`linux/`) underlie container isolation.
- Related IOC guidance is in `incident-response/kubernetes-iocs.md`.

---
