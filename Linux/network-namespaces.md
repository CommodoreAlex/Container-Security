# Network Namespaces

## Overview

Network namespaces are a feature of the Linux kernel that provide isolation of networking resources (interfaces, routing tables, firewall rules, etc.) between processes.  
They are one of the fundamental building blocks of container networking and isolation.

Each network namespace has its own:

- Network interfaces (loopback, veth pairs, physical NICs)
    
- IP addresses
    
- Routing tables
    
- Firewall (iptables/nftables) rules
    

This allows multiple containers (or processes) on the same host to use overlapping IP spaces without interfering with each other.

---

## Why They Matter for Security

- **Attack Surface**: Misconfigured namespaces may allow cross-container traffic or escape paths.
    
- **Auditing**: By inspecting network namespaces, analysts can identify malicious lateral movement, rogue interfaces, or persistence mechanisms.
    
- **Forensics**: Knowing how to enumerate and interact with namespaces is essential for incident response.

---

## Working with Network Namespaces

### 1. List Existing Network Namespaces

```bash
ip netns list
```

- This shows all network namespaces on a host.
- Containers (Docker, Podman, Kubernetes pods) often create their own namespaces.

### 2. Create a New Network Namespace

```bash
sudo ip netns add testns
```

Verify it exists:
```bash
ip netns list
```

### 3. Inspect the Namespace

```bash
sudo ip netns exec testns ip addr
sudo ip netns exec testns ip route
```

- `ip addr`: Shows network interfaces inside the namespace.
- `ip route`: Displays routing table entries.

### 4. Add a Virtual Ethernet (veth) Pair

To connect the namespace to the host:

```bash
sudo ip link add veth0 type veth peer name veth1
sudo ip link set veth1 netns testns
```

- `veth0` stays on the host.
    
- `veth1` moves into the namespace.
    

Assign IPs:

```bash
sudo ip addr add 10.200.1.1/24 dev veth0
sudo ip netns exec testns ip addr add 10.200.1.2/24 dev veth1
```

Bring interfaces up:

```bash
sudo ip link set veth0 up
sudo ip netns exec testns ip link set veth1 up
```
Test connectivity:

```bash
ping -c 2 10.200.1.2
sudo ip netns exec testns ping -c 2 10.200.1.1
```

### 5. Deleting the Namespace

```bash
sudo ip netns del testns
```

---

## Auditing & Forensics Tips

- **Check namespaces in use**:
```bash
ip netns list
lsns -t net
```

- **Identify namespace PID** (to correlate to container/process):
```bash
lsns -t net -p <PID>
```

- **Inspect network activity per namespace** (useful in compromise scenarios):

```bash
sudo ip netns exec <namespace> ss -tulwn
sudo ip netns exec <namespace> iptables -L
```
    
- **For Splunk/ELK**: Track syscalls (`setns`, `unshare`, `clone`) — these often indicate namespace creation or manipulation.


---

## Key Takeaways

- Network namespaces isolate networking resources per container or process.
    
- They are a **core component of container security**.
    
- Auditors should be able to **create, inspect, and map** namespaces to containers/processes to detect anomalies.
