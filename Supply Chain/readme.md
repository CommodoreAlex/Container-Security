# Supply Chain Security

## Overview
Focuses on **container supply chain security**, including image signing, SBOM generation, and CI/CD pipeline protection.  
This section ensures that containerized applications are trustworthy from build to deployment.

## Contents
- **usage.md** – Minimal introduction to image building, signing, and SBOM concepts.  
- **image-signing.md** – Using Cosign or Notary to sign container images.  
- **sbom.md** – Generating and verifying Software Bill of Materials.  
- **grype.md** – Vulnerability scanning with Grype for Linux and container images.  
- **syft.md** – SBOM generation with Syft for Linux packages and container images.  
- **ci-cd-security.md** – Best practices for secure CI/CD pipelines.  
- **examples/** – Scripts and GitHub Actions workflows demonstrating supply chain security. 

## Cross-References
- Container runtime guidance: `docker/`, `podman/`, `kubernetes/`.
- IOC detection: `incident-response/supply-chain-iocs.md`.

---
