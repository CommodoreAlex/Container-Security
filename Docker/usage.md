# Docker Usage Guide

## Overview

This guide introduces the **Docker CLI, images, containers, and registries**.  
It is intended for beginners and auditors who need a practical understanding of Docker fundamentals before applying security practices.

---

## 1. Docker CLI Basics

Check Docker version:

```bash
docker version
```

Check system info:

```bash
docker info
```

---

## 2. Images

### 2.1 Pulling an Image

```bash
docker pull alpine:latest
```

### 2.2 Listing Images

```bash
docker images
```

### 2.3 Removing Images

```bash
docker rmi <image_id_or_name>
```

---

## 3. Containers

### 3.1 Running a Container

```bash
docker run --name mycontainer -it alpine sh
```

- `--name` → assign container name
    
- `-it` → interactive terminal
    
- `alpine` → image
    
- `sh` → command to execute
    

### 3.2 Listing Containers

```bash
docker ps        # Running containers
docker ps -a     # All containers
```

### 3.3 Stopping and Removing Containers

```bash
docker stop mycontainer
docker rm mycontainer
```
---

## 4. Docker Registries

- **Docker Hub** is the default registry: `docker pull <image>`
    
- Private registries can be used:
    

```bash
docker login <registry_url>
docker pull <registry_url>/<image_name>
```

---

## 5. Container Interaction

- **Inspect a container**:
    

```bash
docker inspect mycontainer
```

- **View logs**:
    

```bash
docker logs mycontainer
```

- **Execute commands inside a running container**:
    

```bash
docker exec -it mycontainer sh
```

---

## 6. Practical Tips

- Always tag images explicitly: `alpine:3.18`
    
- Remove unused images and containers:
    

```bash
docker system prune -f
```

- Use `docker diff` to see changes inside a container (useful for auditing).
