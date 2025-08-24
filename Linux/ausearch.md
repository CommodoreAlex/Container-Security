# Comprehensive Guide to `ausearch`

## Overview

`ausearch` is a command-line tool in the **Linux Audit framework** (`auditd`) that queries audit logs for **security-relevant system events**.  
It is useful for auditing user actions, file access, syscalls, process events, and other system activity.

Audit logs are located at:

```bash
/var/log/audit/audit.log
```

Each log event includes a **timestamp, user ID, process ID, syscall, and outcome**. `ausearch` filters and presents these events in a readable form.

---

## 1. Basic Syntax

```bash
ausearch [options]
```
### Common Options

|Option|Description|
|---|---|
|`-sc <syscall>`|Filter by syscall name (`execve`, `capset`, etc.)|
|`-p <pid>`|Filter by process ID|
|`-ua <uid>`|Filter by user ID|
|`-m <type>`|Filter by message type (`USER_CMD`, `SYSCALL`, `LOGIN`)|
|`-k <key>`|Filter by audit rule key|
|`-ts <start>`|Start time (`today`, `1 hour ago`, `YYYY-MM-DD HH:MM:SS`)|
|`-te <end>`|End time|
|`-i`|Interpret numeric IDs for users, groups, and capabilities|
|`-x`|Include executable path info|

---

## 2. Time Filtering

- **Relative times:**
    

```bash
ausearch -ts "1 hour ago" -i
```

- **Absolute times:**
    

```bash
ausearch -ts "2025-08-24 08:00:00" -te "2025-08-24 12:00:00" -i
```

---

## 3. Searching by Syscall

```bsah
ausearch -sc execve -i
```

- Tracks executed commands (`execve` syscall).
    
- Useful for monitoring process execution, security events, or abnormal system calls.
    

```bash
ausearch -sc setxattr -i
```

- Monitors extended attribute changes, often used in SELinux or integrity controls.
    

---

## 4. Searching by User or Process

- Filter by user ID:
    

```bash
ausearch -ua 1000 -i
```

- Filter by process ID:
    

```bash
ausearch -p 1234 -i
```

- Combined filtering:
    

```bash
ausearch -sc execve -ua 1000 -ts today -i
```

---

## 5. Filtering by Audit Rule Key

- Assign a key to audit rules for easier queries:
    

```bash
auditctl -w /etc/passwd -p wa -k passwd_changes
```

This is essentially the ability to create custom queries in order to streamline your workflow. This would become valuable as standard operating procedures evolve. Then BASH could be the key to IT automation where outputs to determine anomaly detection is concerned. 

- Then search with:
    

```bash
ausearch -k passwd_changes -i
```

---

## 6. Searching by Message Type

- Common types:
    
    - `USER_CMD` → commands executed by users
        
    - `SYSCALL` → system calls
        
    - `LOGIN` → login/logout events
        
    - `CRED_ACQ` → credential acquisition
        
    - `CWD` → current working directory at syscall
        

Example:

```bash
ausearch -m USER_CMD -ts today -i
```

---

## 7. Advanced Usage

### 7.1 Combining Multiple Criteria

```bash
ausearch -sc execve -ua 1000 -m USER_CMD -ts today -te now -i
```
### 7.2 Exporting Results

```bash
ausearch -sc execve -ts today -i > /tmp/audit_execve.log
```

### 7.3 Generate Summaries with `aureport`

```bash
aureport -sc --summary
```

- Provides counts of syscalls by type, user, or process.

### 7.4 Track Login and Authentication Events

```bash
ausearch -m LOGIN -ts yesterday -i
```

- Useful for auditing authentication attempts and user sessions.

### 7.5 Detect File Access or Changes

```bash
ausearch -f /etc/shadow -i
```

- Works with file watches defined by `auditctl -w`.

---

## 8. Best Practices

- Always use `-i` to interpret numeric IDs.
    
- Combine **time ranges, syscalls, users, and keys** for precise searches.
    
- Export results to SIEM or log analysis tools for centralized monitoring.
    
- Baseline normal system behavior to spot anomalies efficiently.
    

---

## 9. References

- [ausearch Manual](https://linux.die.net/man/8/ausearch)
