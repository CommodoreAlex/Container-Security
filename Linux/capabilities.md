# Linux Capabilities

## Overview

Linux capabilities allow breaking the all-powerful **root privileges** into fine-grained permissions.  
Containers often run processes as root, but capabilities let you **limit what these processes can do**, reducing the risk of privilege escalation, container escape, or host compromise.

Each capability grants a **specific permission**, like managing networking or changing file ownership, instead of full root access.

---

## Key Concepts

- **Full root privileges** are composed of multiple capabilities.
    
- Common container-relevant capabilities:
    
    - `CAP_NET_ADMIN` → manage networking (interfaces, routes, firewall rules)
        
    - `CAP_SYS_ADMIN` → broad administrative powers (mounts, cgroups, devices)
        
    - `CAP_SYS_PTRACE` → trace other processes (debug or inspect memory)
        
    - `CAP_CHOWN`, `CAP_DAC_OVERRIDE` → bypass filesystem permissions
        
- Capabilities are inherited by processes within containers unless explicitly dropped.
    

---

## Default Capability Sets

- Docker, Podman, and Kubernetes assign a **default capability set** to containers.
    
- Example: Check capabilities inside a running container:
    

```bash
docker run --rm alpine capsh --print
```

- Some capabilities, like `CAP_SYS_ADMIN`, are **high-risk** and should be avoided in untrusted containers.
    

---

## Hardening Best Practices

- Drop all unnecessary capabilities:
    

```bash
docker run --rm --cap-drop=ALL --cap-add=CHOWN alpine sh
```

- Avoid granting `CAP_SYS_ADMIN` or `CAP_NET_ADMIN` unless required.
    
- Use **security benchmarking tools** [(e.g., Docker Bench Security)](https://github.com/docker/docker-bench-security) to audit capability usage.
    

---

## Hands-On / Auditing

### 1. List Capabilities of a Running Container

```bash
docker inspect --format '{{.HostConfig.CapAdd}} {{.HostConfig.CapDrop}}' <container_id>
```

### 2. Enumerate Capabilities Inside a Container

```bash
capsh --print
```

### 3. Drop All But Essential Capabilities

```bash
docker run --rm --cap-drop=ALL --cap-add=CHOWN alpine sh
```

---

## Forensics & IOC Hunting

### 1. Monitor Processes for Excessive Capabilities

```bash
ps -eo pid,user,cap_eff,cmd
```

- `cap_eff` shows **effective capabilities** for each process.
    
- Processes with `CAP_SYS_ADMIN` or `CAP_NET_ADMIN` in untrusted containers may indicate **privilege escalation or escape attempts**.
    

### 2. Audit New Containers

- Track containers started with elevated capabilities using:
    

```bash
docker events --filter 'event=start' --filter 'type=container'
```
- Alert on containers with capabilities beyond baseline defaults.
    

### 3. Integrate `auditd` for Comprehensive Tracking

#### Step 1: Define Audit Rules

Monitor capability changes and sensitive syscalls:

```bash
# Track capset syscalls
auditctl -a always,exit -F arch=b64 -S capset
auditctl -a always,exit -F arch=b32 -S capset

# Track setcap changes (file capabilities)
auditctl -w /usr/bin -p x -k cap_changes
```

#### Step 2: Query Events Using `ausearch`

- Search for capability modification attempts:
    

```bash
ausearch -sc capset -i
```

- Search for file capability changes:
    

```bash
ausearch -k cap_changes -i
```

#### Step 3: Detect Suspicious Behavior

- Look for unexpected granting of `CAP_SYS_ADMIN` or `CAP_NET_ADMIN`.
    
- Monitor processes in untrusted containers performing `capset`.
    
- Correlate with cgroups and namespaces to detect **container breakout attempts**.
    

#### Step 4: Combine with System Call Monitoring

- For broader context, track:
    

```bash
ausearch -sc setns -i
ausearch -sc unshare -i
```

- These syscalls are often used in container escapes or privilege escalation attempts.
    

---

### 4. Best Practices for Audit Logging

- Always use `-i` (interpret) for human-readable logs.
    
- Combine `ausearch` results with SIEM tools (Splunk, ELK) for **real-time detection**.
    
- Regularly review and baseline expected container behavior to reduce false positives.
    

---

## Summary

Linux capabilities enforce **least-privilege principles** inside containers.

- Dropping unnecessary capabilities reduces the **attack surface**.
    
- Auditing and monitoring capabilities with **`auditd` + `ausearch`** helps detect **malicious activity or misconfigurations**.
    
- Integrating capability monitoring with **cgroups and namespaces** strengthens **overall container security** and supports **forensic investigations**.
