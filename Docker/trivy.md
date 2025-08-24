
# Trivy – Container and Artifact Vulnerability Scanning

## Overview

[Trivy](https://github.com/aquasecurity/trivy) (by Aqua Security) is a **comprehensive vulnerability scanner** for containers, filesystem artifacts, cloud infrastructure, and Kubernetes.  
It is lightweight, easy to use, and commonly adopted in **DevSecOps pipelines** and **audit assessments**.

Trivy scans for:

- OS package vulnerabilities (Debian, Alpine, CentOS, etc.)
    
- Language-specific dependencies (Python, Java, Go, Rust, Node.js, etc.)
    
- Misconfigurations (Dockerfiles, Kubernetes YAML, Terraform, AWS CloudFormation)
    
- Secrets (hardcoded credentials, keys, tokens)
    
- SBOM (Software Bill of Materials) generation
    

---

## Installation

### Linux / macOS (script install)

```
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sudo sh
```

### Docker (no local install required)

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image python:3.9
```

---

## Usage Scenarios

### 1. Scanning Docker Images

```bash
trivy image nginx:latest
```

- Reports OS and library vulnerabilities.
    
- Default output: lists CVEs with severity, package, and fixed version.
    

With JSON output (useful for CI pipelines):

```bash
trivy image -f json -o results.json nginx:latest
```

Filter by severity:

```bash
trivy image --severity HIGH,CRITICAL nginx:latest
```

### 2. Scanning Local Filesystems

```bash
trivy fs /path/to/project
```

- Useful for scanning application code, configuration, and secrets.
    
- Detects `requirements.txt`, `package-lock.json`, etc.
    

### 3. Scanning Git Repositories

```bash
trivy repo https://github.com/example/repo.git
```

- Scans remote repositories without local cloning.
    
- Supports CI/CD pre-commit checks.
    

### 4. Scanning for Misconfigurations

```bash
trivy config ./k8s/
```

- Detects Kubernetes, Terraform, and Dockerfile misconfigurations.
    
- Examples: privileged containers, missing resource limits, open security groups.
    

### 5. Scanning SBOMs

```bash
trivy sbom --format cyclonedx --output sbom.json nginx:1.21
```

- Generates SBOMs in **CycloneDX** or **SPDX** formats.
    
- Supports compliance reporting and audit trails.
    

---

## Integration in CI/CD

Example GitHub Actions workflow:

```bash
name: Trivy Scan
on: [push, pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Run Trivy Scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: nginx:latest
          format: 'sarif'
          output: 'trivy-results.sarif'
```

Other integrations:

- GitLab CI
    
- Jenkins
    
- ArgoCD
    
- Tekton
    

---

## Auditor’s Notes

When auditing Trivy reports, pay attention to:

- **Severity levels:** LOW → CRITICAL
    
- **Fix availability:** Prioritize vulnerabilities with known patches.
    
- **False positives:** Occasionally Trivy may flag unexploitable paths; cross-reference with application usage.
    
- **Baseline scans:** Capture a point-in-time report for compliance.
    
- **Drift monitoring:** Compare SBOMs between builds to detect new risk introduction.
    

---

## Best Practices

- Always pin image versions (`nginx:1.21`, not `nginx:latest`).
    
- Automate scans in CI/CD for every build and push.
    
- Periodically rescan images, as vulnerabilities are disclosed after image release.
    
- Combine with **docker-bench-security** for host-level hardening.
    
- Export reports in JSON/SARIF for central dashboards (DefectDojo, Security Hub).
