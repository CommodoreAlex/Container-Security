# Control Groups (cgroups)

## Overview

Control groups (cgroups) are a Linux kernel feature that allows you to **limit, isolate, and monitor resource usage** (CPU, memory, I/O, etc.) of processes. Containers heavily rely on cgroups to enforce resource boundaries and prevent noisy-neighbor issues.

From a **security perspective**, cgroups help prevent **resource exhaustion attacks** (e.g., fork bombs or runaway processes) by constraining processes inside a container.

---

## Key Capabilities

- **Resource Limiting**: Constrain CPU, memory, or disk usage of a process/container.
    
- **Prioritization**: Allocate more resources to critical processes.
    
- **Accounting**: Track how much resources processes consume.
    
- **Isolation**: Ensure a process doesn’t starve other workloads.
    

---

## Types of Resources Controlled

- **CPU** – restricts or prioritizes processor time.
    
- **Memory** – caps memory usage and triggers OOM kills if exceeded.
    
- **Block I/O** – throttles read/write to disk.
    
- **Network (via tc & cgroup classifiers)** – manage bandwidth.
    

---

## Hands-On: Creating and Auditing cgroups

### 1. Check cgroup version

```bash
mount | grep cgroup
```

- `cgroup` → v1
    
- `cgroup2` → unified v2
    

Most modern distros (systemd-based) use **cgroup v2**.

---

### 2. Create a new cgroup (CPU example)

```bash
# Create a cgroup
sudo mkdir /sys/fs/cgroup/mygroup

# Add a process (replace PID with real process ID)
echo <PID> | sudo tee /sys/fs/cgroup/mygroup/cgroup.procs
```

---

### 3. Limit CPU usage

```bash
# Allow only 20% CPU
echo 20000 | sudo tee /sys/fs/cgroup/mygroup/cpu.max
```

---

### 4. Limit Memory

```bash
# Limit memory to 100MB
echo 100M | sudo tee /sys/fs/cgroup/mygroup/memory.max
```

---

### 5. Monitor Usage

```bash
# CPU stats
cat /sys/fs/cgroup/mygroup/cpu.stat

# Memory usage
cat /sys/fs/cgroup/mygroup/memory.current
```

---

## Auditing & Security Use Cases

Cgroups provide rich information for **auditing, hunting, and incident response**.

### 1. Detecting Resource Abuse

Attackers or misconfigured containers may bypass orchestrator limits or consume excessive resources.

**Example: Check for high CPU usage**

```bash
for cg in $(find /sys/fs/cgroup/ -type d -name "*"); do
    cpu_usage=$(cat $cg/cpu.stat 2>/dev/null | grep usage_usec | awk '{print $2}')
    if [ "$cpu_usage" -gt 100000000 ]; then
        echo "High CPU in $cg: $cpu_usage µs"
    fi
done
```

**Example: Check memory usage vs limit**

```bash
for cg in $(find /sys/fs/cgroup/ -type d -name "*"); do
    mem=$(cat $cg/memory.current 2>/dev/null)
    mem_max=$(cat $cg/memory.max 2>/dev/null)
    if [ "$mem_max" != "max" ] && [ "$mem" -gt $mem_max ]; then
        echo "Memory exceeded in $cg: $mem / $mem_max"
    fi
done
```

---

### 2. Hunting Suspicious Processes

- **Processes appearing in multiple cgroups** may indicate container escape or injection attempts:
    

```bash
for pid in $(ps -eo pid); do
    groups=$(grep -H '' /proc/$pid/cgroup 2>/dev/null)
    if [ $(echo "$groups" | wc -l) -gt 1 ]; then
        echo "Process $pid in multiple cgroups:"
        echo "$groups"
    fi
done
```

---

### 3. Monitoring CPU Throttling

High CPU throttling may indicate resource DoS or abnormal usage:
```bash
for cg in $(find /sys/fs/cgroup/ -type d -name "*"); do
    throttled_usec=$(grep throttled_usec $cg/cpu.stat 2>/dev/null | awk '{print $2}')
    if [ "$throttled_usec" -gt 10000000 ]; then
        echo "Potential CPU throttling in $cg: $throttled_usec µs"
    fi
done
```

---

### 4. Memory Forensics

- Track **OOM events**:
    
```bash
for cg in $(find /sys/fs/cgroup/ -type d -name "*"); do
    oom_events=$(cat $cg/memory.oom.events 2>/dev/null)
    if [ "$oom_events" != "0" ]; then
        echo "OOM events detected in $cg: $oom_events"
    fi
done
```

- Spikes may indicate malicious memory exhaustion or misconfigured containers.
    

---

### 5. Practical Tips

- **Map cgroups to containers** to correlate findings with container IDs.
    
- **Baseline normal resource usage** to detect anomalies faster.
    
- **Automate monitoring** via scripts or SIEM/Splunk.
    
- **Combine with audit logs** for deeper detection (`clone`, `setns`, `unshare` syscalls).
    

---

## Summary

Cgroups are foundational to container security, providing **resource isolation, usage auditing, and attack prevention**.  
Security teams should monitor cgroups as part of their **detection strategy** to spot anomalies or misconfigurations.
