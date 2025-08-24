# Grype: Vulnerability Scanning on Linux

## Overview

[Grype](https://github.com/anchore/grype) is an open-source vulnerability scanner for **container images and filesystems**, built by Anchore.  
It detects vulnerabilities using OS package databases (Debian, Ubuntu, CentOS, Alpine, etc.) and language-specific package managers (npm, pip, gem, etc.).

Grype integrates well into CI/CD pipelines, supports **SBOM inputs**, and is commonly used alongside [Syft](https://github.com/anchore/syft) for software bill of materials (SBOM) generation.

---

## Installation on Linux

Grype provides static binaries for easy setup.

```bash
# Download latest release
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sudo sh -s -- -b /usr/local/bin

# Verify installation
grype version
```

Alternative installation via package managers:

`# Using Homebrew on Linux brew tap anchore/grype brew install grype`

---

## Basic Usage

### 1. Scan a Container Image

```bash
# Scan a local Docker image
grype ubuntu:20.04

# Scan an image from a remote registry
grype nginx:latest
```

### 2. Scan a Filesystem

```bash
# Scan the root filesystem
sudo grype dir:/

# Scan a project directory
grype dir:/home/user/myapp
```

### 3. Scan from an SBOM

Grype supports CycloneDX and Syft SBOMs.

```bash
# Generate SBOM with syft
syft myapp:latest -o cyclonedx-json > sbom.json

# Scan SBOM with grype
grype sbom:sbom.json
```

---

## Output Formats

Grype supports multiple output formats for integration.

```bash
# Default human-readable
grype alpine:3.17

# JSON output for pipelines
grype alpine:3.17 -o json

# Table output
grype alpine:3.17 -o table
```

---

## Integration into CI/CD

Grype can fail builds when vulnerabilities exceed a severity threshold.

```bash
# Fail if a Critical vuln is found
grype myapp:latest --fail-on critical
```

Common CI/CD integrations:

- **GitHub Actions**: Anchore publishes a [grype-action](https://github.com/marketplace/actions/scan-for-vulnerabilities-with-grype).
    
- **GitLab CI/CD**: Add as a stage to scan built images before deployment.
    
- **Jenkins**: Run Grype inside a pipeline shell step.
    

---

## Updating the Database

Grype relies on vulnerability databases (NVD, distro-specific advisories). Keep it updated:

```bash
# Update vulnerability database
grype db update

# Show DB status
grype db status
```

---

## Best Practices

- Use **Syft + Grype together**: generate SBOMs with Syft, scan with Grype.
    
- Integrate into **pre-deployment checks** (CI/CD pipelines).
    
- Run regular **host scans** (`grype dir:/`) for system-level packages.
    
- Automate database updates with cron:
    
```bash
0 3 * * * grype db update
```
    
- Use machine-readable formats (JSON, CycloneDX) for compliance reporting.
