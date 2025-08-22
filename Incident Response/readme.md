# Incident Response & Indicators of Compromise (IOCs)

## Overview
This section focuses on **detection and response** in containerized environments.  

While the rest of the repository emphasizes prevention and hardening, the artifacts here address how to **identify, investigate, and respond** to suspicious or malicious activity across container platforms.

The content is organized by component (e.g., Docker, Kubernetes, Rancher) and includes references to host-level and network indicators.  

Where applicable, example queries and dashboards for **Splunk** are provided to illustrate how indicators can be operationalized in monitoring platforms.

---

## Directory Structure
```text
incident-response/
├── overview.md                # Role of detection/response in container environments
├── docker-iocs.md             # Docker daemon logs, container runtime artifacts
├── kubernetes-iocs.md         # K8s audit logs, API server logs, Falco alerts
├── podman-iocs.md             # Podman events/logs
├── rancher-iocs.md            # Rancher audit logs, RBAC abuse indicators
├── orchestrators-iocs.md      # Helm/ArgoCD/Swarm compromise indicators
├── host-iocs.md               # Linux host logs, syscalls, journald, kernel messages
├── network-iocs.md            # Suspicious east-west traffic, CNI logs, DNS exfiltration
└── splunk-queries/              # Focused query library for IOC detection
```

## Usage

Platform-Specific IOC Guides
- Each *-iocs.md file provides examples of suspicious log entries, behaviors, or conditions relevant to that component (e.g., Docker daemon socket abuse, Kubernetes API privilege escalation).

Host & Network Coverage
- Since containers ultimately rely on the Linux kernel, host-level and network-level IOCs are also included to give defenders visibility outside the orchestrator’s view.

Splunk Dashboards
- Example queries and dashboards are provided in JSON format for common SIEM platforms. These can be adapted to your environment to accelerate monitoring and alerting.

---
