# Linux Primitives for Container Security

## Overview

This section covers the foundational Linux features that enable containerization: **namespaces, cgroups, and capabilities**.  
Understanding these primitives is essential for analyzing container isolation, resource controls, and potential security risks.

## Contents
- **usage.md** – Minimal introduction to using namespaces, cgroups, and capabilities.
- **namespaces.md** – Detailed explanation of PID, NET, MNT, IPC, UTS, USER namespaces.
- **cgroups.md** – Resource limits, controlling CPU/memory to mitigate DoS attacks.
- **capabilities.md** – Managing Linux capabilities to reduce privilege exposure.

## Cross-References
- See `docker/usage.md` and `podman/usage.md` for how these primitives are applied in container runtimes.

---
