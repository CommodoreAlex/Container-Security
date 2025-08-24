# syft.md – SBOM Generation with Syft

## Overview
[Syft](https://github.com/anchore/syft) is an open-source CLI tool and Go library for generating a **Software Bill of Materials (SBOM)** from container images, filesystems, or directories.  
It provides visibility into the dependencies and libraries in your software supply chain, which is essential for security and compliance.

---

## Why Syft?
- **Visibility** – Know exactly what’s in your images and binaries.
- **Compliance** – Generate SBOMs in industry formats like SPDX, CycloneDX, and Syft’s native JSON.
- **Interoperability** – Works seamlessly with other tools (e.g., [Grype](./grype.md)) for vulnerability scanning.
- **Automation** – Integrates into CI/CD pipelines for continuous monitoring.

---

## Installation (Linux)

### Binary Install
```bash
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sudo sh -s -- -b /usr/local/bin
```

### Verify

```bash
syft version
```

---

## Basic Usage

### Scan a Container Image

```bash
syft ubuntu:20.04
```

Generates a package inventory of all libraries and dependencies in the Ubuntu 20.04 image.

### Scan a Local Filesystem

```bash
syft dir:/path/to/project
```

Useful for SBOMs of source code repositories or built applications.

---

## Output Formats

Syft supports multiple SBOM formats. Use the `-o` flag to specify:

```
syft ubuntu:20.04 -o table          # Human-readable
syft ubuntu:20.04 -o json           # Raw JSON
syft ubuntu:20.04 -o cyclonedx-json # CycloneDX JSON
syft ubuntu:20.04 -o spdx-json      # SPDX JSON
syft ubuntu:20.04 -o syft-json      # Syft’s native JSON
```

---

## Storing and Sharing SBOMs

Save output to a file:

```bash
syft nginx:latest -o spdx-json > nginx.spdx.json
```

Upload SBOMs to artifact repositories or registries (e.g., GitHub Packages, Harbor, AWS ECR).

---

## Integration with Grype

Syft works natively with Grype for vulnerability scanning.  
You can pipe Syft output directly into Grype:

```bash
syft nginx:latest -o json | grype
```

---

## CI/CD Pipeline Example (GitHub Actions)

```bash
name: Build and SBOM
on: [push]

jobs:
  sbom:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Install Syft
        run: curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sudo sh -s -- -b /usr/local/bin

      - name: Generate SBOM
        run: syft dir:. -o cyclonedx-json > sbom.json

      - name: Upload SBOM as artifact
        uses: actions/upload-artifact@v3
        with:
          name: sbom
          path: sbom.json
```
---

## Best Practices

- Always generate SBOMs during **build time** to capture dependencies before deployment.
    
- Use **CycloneDX or SPDX** for interoperability across security tools.
    
- Store SBOMs in your **artifact registry** alongside the container images.
    
- Combine Syft with **Grype** for full visibility into vulnerabilities.
    
- Automate SBOM generation in CI/CD pipelines for continuous supply chain security.
