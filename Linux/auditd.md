# Linux Audit Daemon (auditd) Guide

## Overview

The **Linux Audit Daemon (`auditd`)** is a **kernel-level auditing system** that logs security-relevant events, such as system calls, file access, process execution, and user activity.  
It forms the foundation for **forensic investigation, compliance auditing, and intrusion detection**.

---

## 1. Installation

Install auditd using your package manager:

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install auditd audispd-plugins

# RHEL/CentOS/Fedora
sudo yum install audit audit-libs audit-libs-python
sudo systemctl enable --now auditd
```

Check status:

```bash
sudo systemctl status auditd
```

---

## 2. Configuration

Configuration file: `/etc/audit/auditd.conf`

Key parameters:

|Parameter|Description|
|---|---|
|`log_file`|Path to audit log (default: `/var/log/audit/audit.log`)|
|`log_format`|`RAW` or `ENRICHED` formats|
|`max_log_file`|Max log file size in MB|
|`max_log_file_action`|Action on max log size (`rotate`, `ignore`, `suspend`)|
|`space_left`|Threshold to alert on disk usage|
|`admin_space_left_action`|Action when disk space is critically low (`email`, `exec`, `shutdown`)|

---

## 3. Audit Rules

Audit rules define what events are **monitored**.

### 3.1 Temporary Rules

```bash
# Watch /etc/passwd for writes
sudo auditctl -w /etc/passwd -p wa -k passwd_changes

# Track execve system calls
sudo auditctl -a always,exit -F arch=b64 -S execve -k exec_log
```

### 3.2 Persistent Rules

Add rules in `/etc/audit/rules.d/audit.rules`:

```bash
-w /etc/shadow -p wa -k shadow_changes
-a always,exit -F arch=b64 -S setxattr -k attr_changes
```

Reload rules:

```bash
sudo augenrules --load
```

---

## 4. Querying Logs

`ausearch` is used to query audit logs.

### Examples:

```bash
# Search by key
ausearch -k passwd_changes -i

# Search by syscall
ausearch -sc execve -ts today -i

# Search by user
ausearch -ua 1000 -i
```

---

## 5. Summaries and Reports

`aureport` generates **aggregated reports**:

```bash
# Summary of all syscalls
aureport -sc

# User login summary
aureport -l

# File access reports
aureport -f
```

---

## 6. Integration with Security Monitoring

- Combine `auditd` logs with **SELinux AVCs**, `ausearch`, and container monitoring tools.
    
- Export logs to **SIEM** for centralized analysis.
    
- Track IOCs such as:
    
    - Unauthorized capability changes
        
    - Suspicious system call patterns
        
    - Unauthorized file or directory modifications
        

---
