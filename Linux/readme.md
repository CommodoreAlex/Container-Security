# Linux Primitives for Container Security

## Overview

This section covers the foundational Linux features that enable containerization: **namespaces, cgroups, capabilities, and SELinux**.  
Understanding these primitives is essential for analyzing container isolation, resource controls, mandatory access controls, and potential security risks.

---

## Contents

- **ausearch.md** – Advanced IT security auditing with `ausearch`.  
- **namespaces.md** – Detailed explanation of PID, NET, MNT, IPC, UTS, and USER namespaces.  
- **cgroups.md** – Resource limits, controlling CPU/memory to mitigate DoS attacks.  
- **capabilities.md** – Managing Linux capabilities to reduce privilege exposure.  
- **selinux.md** – Understanding SELinux, auditing AVC denials, managing policies, and administrative tasks for security monitoring.  

## Cross-References
- See `docker/usage.md` and `podman/usage.md` for how these primitives are applied in container runtimes.

---
