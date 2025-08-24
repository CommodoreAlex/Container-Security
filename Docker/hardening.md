# Docker Hardening Guide

## Purpose

This document provides **detailed best practices for hardening Docker hosts, daemons, and containers** to reduce the attack surface, enforce least privilege, and support compliance with security standards (e.g., CIS, NIST, PCI DSS, HIPAA).

It is designed for:

- **Security engineers** — to assess and enforce controls.
    
- **System administrators** — to securely operate Docker environments.
    
- **Auditors/compliance teams** — to evaluate adherence to baselines.
    

---

## 1. Host Hardening

### 1.1 Minimal Base OS

- Deploy Docker on **dedicated, minimal distributions** (e.g., Ubuntu Minimal, RHEL CoreOS, Bottlerocket).
    
- Avoid installing compilers, browsers, or unnecessary services on the host.
    
- Apply CIS Linux benchmarks.
    

### 1.2 Patch & Update Management

- Automate patching via a configuration management tool (e.g., Ansible, Puppet, SaltStack).
    
- Track kernel CVEs (e.g., Dirty COW, Spectre, Meltdown).
    
- Reboot hosts when kernel-level vulnerabilities are patched.
    

### 1.3 Restrict Docker Daemon Access

- By default, Docker’s Unix socket (`/var/run/docker.sock`) gives **root-equivalent access**.
    
- Only allow trusted users:
    

```bash
sudo groupadd docker
sudo usermod -aG docker <trusted_user>
```

- Never expose the Docker API over TCP without TLS and authentication.
    

### 1.4 Kernel & OS Security

- Enforce kernel hardening:
    

```bash
# Disable IP forwarding by default
sysctl -w net.ipv4.ip_forward=0
# Restrict core dumps
sysctl -w fs.suid_dumpable=0
# Enable address space layout randomization
sysctl -w kernel.randomize_va_space=2
```

- Use **SELinux**, **AppArmor**, or **Seccomp** for mandatory access control.
    
- Enable **auditd** to log privileged Docker activity.
    

---

## 2. Docker Daemon Hardening

### 2.1 Run in Rootless Mode (Preferred)

- Rootless mode prevents containers from directly interacting with host root.
    
- Installation:
    

```bash
curl -fsSL https://get.docker.com/rootless | sh
```

### 2.2 Configure Daemon Flags

- Harden `dockerd` with `/etc/docker/daemon.json`:
    

```bash
{
  "icc": false, 
  "no-new-privileges": true,
  "userns-remap": "default",
  "live-restore": true,
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

**Key Flags**:

- `icc=false` → disables inter-container communication by default.
    
- `userns-remap=default` → maps container root to unprivileged host UID.
    
- `live-restore=true` → keeps containers running when daemon restarts.
    
- `no-new-privileges` → blocks privilege escalation.
    

### 2.3 TLS for API

- If remote API is required, enforce mutual TLS:
    

```bash
dockerd --tlsverify \
  --tlscacert=ca.pem \
  --tlscert=server-cert.pem \
  --tlskey=server-key.pem \
  -H=0.0.0.0:2376
```

---

## 3. Container Hardening

### 3.1 Image Security

- Use **trusted base images** from official registries.
    
- Pin versions and digests instead of `latest`:
    

```bash
docker pull nginx@sha256:<digest>
```

- Remove package managers, shells, and compilers from production images.
    
- Scan images with **Trivy** or **Clair** before deployment (see `scanning.md`).
    

### 3.2 Least Privilege Runtime

- Drop capabilities:
    

```bash
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx
```

- Avoid `--privileged`.
    
- Use `--read-only` to prevent filesystem writes:
    

```bash
docker run --read-only -v /tmp tmpfs nginx
```

### 3.3 Resource Constraints

Prevent DoS by enforcing limits:

```bash
docker run --memory=512m --cpus=1 nginx
```

- Use `pids-limit` to prevent fork bombs:
    

```bash
docker run --pids-limit=100 nginx
```

### 3.4 Networking Controls

- Use **user-defined bridges** to isolate workloads.
    
- Apply firewall rules with `iptables` or CNI plugins.
    
- Never bind sensitive containers to `0.0.0.0`.
    
- Use Docker’s built-in secrets management for passwords and API keys.
    

---

## 4. Auditing & Monitoring

### 4.1 Docker Bench Security

- Run [docker-bench-security](https://github.com/docker/docker-bench-security) regularly:
    

```bash
git clone https://github.com/docker/docker-bench-security.git
./docker-bench-security.sh
```

### 4.2 Logging & Visibility

- Centralize logs via ELK/Fluentd.
    
- Enable container process auditing with `auditd`:
    

```bash
auditctl -w /usr/bin/docker -k docker
```

### 4.3 Runtime Threat Detection

- Deploy runtime monitoring tools:
    
    - Falco for syscall anomaly detection.
        
    - Sysdig or Aqua Security for behavioral analysis.
        

---

## 5. Compliance Mapping

|Control Area|Hardening Measure|
|---|---|
|**CIS Docker Benchmark**|Run `docker-bench-security`; configure daemon.json|
|**PCI DSS**|Limit access, monitor logs, encrypt data at rest|
|**HIPAA**|Enforce least privilege; audit data access|
|**NIST 800-190**|Follow secure container deployment checklist|
