# SELinux Guide for Auditing and Administration

## Overview

**SELinux (Security-Enhanced Linux)** is a **mandatory access control (MAC)** system built into Linux.  
It enforces **fine-grained security policies** to restrict what processes can do, beyond traditional DAC (Discretionary Access Control) permissions.

For auditors and administrators, understanding SELinux is crucial because it can **prevent privilege escalation, contain misbehaving processes, and enforce compliance controls**.

---

## 1. SELinux Modes

- **Enforcing** – Policies are enforced, access violations are denied.
    
- **Permissive** – Violations are logged but not blocked. Useful for auditing before enforcement.
    
- **Disabled** – SELinux is turned off. Not recommended for production.
    

Check current mode:

```bash
getenforce
sestatus
```

Change mode temporarily:

```bash
sudo setenforce 1  # Enforcing
sudo setenforce 0  # Permissive
```

---

## 2. SELinux Policy Types

- **Targeted** – Focused protection on critical services (default in most distributions).
    
- **MLS / MCS** – Multi-Level / Multi-Category Security; more restrictive and complex.
    
- **Custom policies** – Can define application-specific rules for containers or services.
    

---

## 3. Contexts and Labels

SELinux uses **labels** to enforce policy:

- **File context:** `ls -Z /etc/passwd`
    
- **Process context:** `ps -eZ`
    

Each context typically has **user:role:type:level**:

```bash
system_u:system_r:sshd_t:s0
```

- **user** – SELinux user (not Linux user)
    
- **role** – Role assignment
    
- **type** – Domain/type for access control
    
- **level** – MLS/MCS level
    

---

## 4. Key SELinux Tools

|Tool|Purpose|
|---|---|
|`getenforce`|Current mode|
|`setenforce`|Switch mode temporarily|
|`sestatus`|Overview of SELinux status|
|`semanage`|Manage policy objects (ports, files, booleans)|
|`restorecon`|Restore file contexts to defaults|
|`chcon`|Change file context temporarily|
|`audit2allow`|Generate policy modules from audit logs|
|`ausearch`|Query SELinux denial events (AVCs)|
|`sealert`|Analyze AVC denials and provide solutions|

---

## 5. Auditing SELinux

SELinux logs **denied accesses** as **AVC (Access Vector Cache) messages**.

- Check denials in logs:
    

```bash
sudo ausearch -m avc -ts today -i
```

- Typical denial:
    

```bash
type=AVC msg=audit(1621234567.123:123): avc:  denied  { write } for  pid=1234 comm="nginx" name="secrets" dev="sda1" ino=123456 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:secrets_t:s0 tclass=file
```

**Components:**

- `scontext` – source (process) context
    
- `tcontext` – target (file, socket, etc.) context
    
- `tclass` – object class (file, dir, socket)
    
- `comm` – process name
    

---

## 6. Hands-On Usage

### 6.1 List SELinux File Contexts

```bash
ls -Z /var/www/html
```

### 6.2 Change File Context

```bash
sudo chcon -t httpd_sys_content_t /var/www/html/index.html
```

### 6.3 Restore Default Contexts

```bash
sudo restorecon -Rv /var/www/html
```

### 6.4 View and Set Booleans

- View:
```bash
getsebool -a | grep httpd
```

- Enable boolean:
```bash
sudo setsebool -P httpd_can_network_connect on
```

---

## 7. Administrative Tasks

- **Managing Ports:**
```bash
semanage port -a -t http_port_t -p tcp 8080
```

- **Managing Users & Roles:**
```bash
semanage login -l
semanage user -a -R "staff_r" auditor_u
```

- **Custom Policy Modules:**
    
    1. Capture denials:
        
    
    ```bash
    ausearch -m avc -ts today -i > avc.log
    ```
    
    2. Generate policy:
        
    
    ```bash
    audit2allow -M mypolicy < avc.log
	semodule -i mypolicy.pp
    ```
    

---

## 8. SELinux for Auditors

- **Check system-wide status:**
```bash
sestatus
```

- **Identify processes in permissive mode:**
```bash
ps -eZ | grep unconfined
```

- **Analyze AVC denials:**
```bash
ausearch -m avc -ts today -i sealert -a /var/log/audit/audit.log
```

- **Correlate denials to processes, services, or containers**.
    
- Ensure **baseline policies** are documented and enforced; deviations may indicate misconfigurations or potential breaches.
    

---

## 9. Best Practices

- Keep SELinux in **enforcing mode** in production.
    
- Regularly audit **AVC denials** to detect misconfigurations.
    
- Use **booleans** for fine-tuning services without creating custom policies unnecessarily.
    
- Record and version **custom policy modules** for auditability.
    
- Combine SELinux logs with **`auditd`** or SIEM for centralized monitoring.
    

---
