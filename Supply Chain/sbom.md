# Software Bill of Materials (SBOM) Guide

## Overview

A **Software Bill of Materials (SBOM)** is a structured inventory of all components, dependencies, and libraries within a piece of software.  
For containerized and cloud-native environments, SBOMs provide **transparency, compliance, and risk visibility** across the supply chain.

SBOM adoption is increasingly mandated by regulations (e.g., **US Executive Order 14028**), standards (e.g., **NIST SSDF, SLSA**), and compliance frameworks (e.g., **CIS, ISO/IEC 27001**).

---

## Why SBOMs Matter

- **Transparency**: Know exactly what’s running in your software and containers.
    
- **Vulnerability Management**: Map vulnerabilities (CVEs) to specific packages.
    
- **Compliance**: Demonstrate regulatory adherence and licensing requirements.
    
- **Incident Response**: Quickly assess impact when new vulnerabilities (e.g., Log4Shell) emerge.
    
- **Supply Chain Defense**: Detect tampering or unapproved components.
    

---

## Key SBOM Standards

- **SPDX (Software Package Data Exchange)** – Maintained by the Linux Foundation. Widely adopted in open source.
    
- **CycloneDX** – Lightweight, security-focused SBOM format developed by OWASP.
    
- **SWID (Software Identification Tags)** – Standardized ISO/IEC approach.
    

---

## Generating SBOMs

### 1. Docker/OCI Images

- **Syft** (by Anchore):
    
    ```bash
    syft myapp:latest -o cyclonedx-json > sbom.json
    ```
    
- **Trivy**:
    
    ```bash
    trivy image --format cyclonedx --output sbom.json myapp:latest
    ```
    
- **Docker Desktop (v4.7+)**: Has built-in SBOM export.
    

### 2. Source Code

- **Syft** can analyze source repos:
    
    ```bash
    syft dir:./myproject -o spdx-json > sbom.json
    ```
    

### 3. CI/CD Pipelines

- Integrate SBOM generation into GitHub Actions, GitLab CI, or Jenkins:
    
    - Run **Syft/Trivy** as a pipeline stage.
        
    - Export SBOM artifacts to security dashboards or compliance storage.
        

---

## Validating and Using SBOMs

### Validation

- **SPDX Tools**: Validate SPDX-compliant SBOMs.
    
- **CycloneDX CLI**:
    
    ```bash
    cyclonedx validate --input-file sbom.json
    ```
    

### Vulnerability Mapping

- **Grype** (Anchore):
    
    ```bash
    grype sbom:sbom.json
    ```
    
- **Trivy with SBOMs**:
    
    ```bash
    trivy sbom sbom.json
    ```
    

### Licensing Audits

- SBOMs also help track **license obligations** (GPL, Apache, MIT).
    
- Tools like **FOSSA** or **Syft** flag risky licenses.
    

---

## Storage and Distribution

- Store SBOMs as artifacts in:
    
    - **Artifact Repositories** (JFrog Artifactory, Harbor, Nexus).
        
    - **Git Repos** (with CI/CD provenance).
        
    - **Registry Metadata** (e.g., OCI artifacts attached to Docker images).
        
- Share SBOMs with:
    
    - **Auditors** for compliance verification.
        
    - **Customers** for transparency.
        
    - **Security Teams** for vulnerability response.
        

---

## SBOM in Incident Response

When a critical CVE emerges (e.g., OpenSSL, Log4j):

1. Search SBOMs for affected dependencies.
    
2. Identify impacted applications/images.
    
3. Patch or rebuild artifacts.
    
4. Document remediation for compliance.
    

---

## Best Practices

- **Automate SBOM generation** in CI/CD (not manual).
    
- **Standardize formats** (CycloneDX or SPDX).
    
- **Version-control SBOMs** for traceability.
    
- **Link SBOMs with attestation/provenance** (SLSA, in-toto).
    
- **Periodically audit** stored SBOMs for aging dependencies.
    

---

## Example Workflow

1. Developer commits code → CI pipeline builds Docker image.
    
2. Pipeline runs **Syft** to generate SBOM in SPDX and CycloneDX.
    
3. SBOM stored in artifact repo + attached to OCI image.
    
4. Pipeline runs **Grype/Trivy** against SBOM to check vulnerabilities.
    
5. Results exported to security dashboard (e.g., DefectDojo, SecurityHub).
    
6. On new CVEs, IR team queries SBOMs to locate exposure.
