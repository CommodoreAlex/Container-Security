# Docker Bench Security Guide

## Overview

[**Docker Bench Security**](https://github.com/docker/docker-bench-security) is a **scripted security audit tool** that checks for best practices around deploying Docker containers.  
It evaluates Docker daemon configuration, container runtime, host OS settings, and Docker networking against **CIS Docker Benchmarks**.

This guide covers **installation, usage, and interpreting audit results**, providing auditors and administrators with actionable insights.

---

## 1. Installation

Docker Bench Security is available as a GitHub repository. Clone it to your host:

```bash
git clone https://github.com/docker/docker-bench-security.git
cd docker-bench-security
```

No compilation is required—it is a **Bash script**.

---

## 2. Running the Script

Run as **root** to allow all checks:

```bash
sudo sh docker-bench-security.sh
```

**Options:**

- `-c <check>` – Run a specific check or group of checks.
    
- `-l` – List all available tests.
    
- `-h` – Display help.
    

---

## 3. Understanding the Tests

The script organizes checks into sections:

1. **Host Configuration**
    
    - Kernel parameters, OS hardening, logging, and auditing.
        
2. **Docker Daemon Configuration**
    
    - TLS usage, user namespaces, logging drivers, default cgroup settings.
        
3. **Docker Images and Containers**
    
    - Verify that containers are using official images, have resource limits, and drop unnecessary capabilities.
        
4. **Docker Security Operations**
    
    - Checks for running privileged containers, container escape risks, and use of sensitive host volumes.
        
5. **Docker Swarm / Kubernetes (if applicable)**
    
    - Node and orchestration security, secret management.
        

---

## 4. Example Run

```bash
sudo sh docker-bench-security.sh | tee docker-audit.log
```

**Output Example:**

```bash
[INFO] 1.1  - Ensure a separate partition for containers
[PASS] 1.2  - Ensure only trusted users are allowed to control Docker daemon
[WARN] 2.1  - Ensure TLS authentication for Docker daemon
[FAIL] 2.2  - Ensure that the Docker daemon is not running as root
...
```

- **PASS** – Compliant
    
- **WARN** – Needs review
    
- **FAIL** – Requires immediate action
    

---

## 5. Interpreting Results

- Focus on **FAIL** items first—these indicate **high-risk security misconfigurations**.
    
- **WARN** items are **potential risks or best practices** to implement over time.
    
- **PASS** confirms that the system complies with the benchmark.
    

---

## 6. Integrating with Auditing

### 6.1 Logging Results

Save output for auditing purposes:

```bash
sudo sh docker-bench-security.sh | tee /var/log/docker-bench-security.log
```

### 6.2 Automating Checks

- Run periodically via **cron**:
    

```bash
0 2 * * 1 /usr/local/bin/docker-bench-security.sh >> /var/log/docker-bench-security.log 2>&1
```

- Integrate with **SIEM or centralized logging** for compliance reporting.
    

### 6.3 Cross-Reference with Other Tools

- Combine with:
    
    - `docker inspect` for container capabilities
        
    - `cgroups` and `namespaces` auditing
        
    - `SELinux` AVC logs via `ausearch`
        

This provides a **full container security posture** view.

---

## 7. Best Practices

- Always run **Docker Bench Security** on a **non-production or snapshot host** if possible.
    
- Keep the script **updated** (`git pull`) to reflect latest CIS benchmarks.
    
- Regularly document **baseline security posture** and track changes over time.
    
- Use results to **enforce policies** for container images, runtime options, and host configurations.
