# Docker - Complete Guide

## Table of Contents
1. [What is Docker](#what-is-docker)
2. [Why We Need Docker](#why-we-need-docker)
3. [Key Docker Concepts](#key-docker-concepts)
4. [Docker Architecture](#docker-architecture)
5. [Docker Installation](#docker-installation)
6. [Basic Docker Commands](#basic-docker-commands)
7. [Docker Images](#docker-images)
8. [Docker Containers](#docker-containers)
9. [Docker Networking](#docker-networking)
10. [Docker Volumes](#docker-volumes)
11. [Docker Compose](#docker-compose)
12. [Best Practices](#best-practices)

---

## What is Docker?

Docker is a lightweight, open-source containerization platform that allows you to package applications and their dependencies into isolated, portable containers. A Docker container is a standalone, executable package that contains everything needed to run an application: code, runtime, system tools, libraries, and settings.

**Key Characteristics:**
- **Containerization**: Wraps applications in containers for consistency across environments
- **Lightweight**: Uses less resources compared to virtual machines
- **Portability**: "Build once, run anywhere" - containers run consistently on any system
- **Isolation**: Each container is isolated from others and the host system
- **Reproducibility**: Ensures the same environment from development to production

---

## Why We Need Docker?

### Problems Docker Solves:

1. **Dependency Hell**: Applications depend on specific versions of libraries. Docker packages everything.
2. **Environment Inconsistency**: "Works on my machine" problem - different environments cause issues.
3. **Resource Efficiency**: Containers share the host OS kernel, making them lightweight vs VMs.
4. **Scalability**: Easily spin up multiple container instances for load balancing.
5. **DevOps Automation**: Integrates seamlessly with CI/CD pipelines.
6. **Microservices Architecture**: Build loosely coupled microservices.

### Benefits:
- Consistency across dev, testing, and production
- Faster deployment cycles
- Simplified dependency management
- Better resource utilization
- Improved security through isolation
- Cost reduction in infrastructure

---

## Key Docker Concepts

### 1. Container
A lightweight, standalone executable package containing:
- Application code
- Runtime environment
- System libraries
- Environment variables

### 2. Image
A blueprint/template for creating containers:
- Built using Dockerfiles
- Versioned using tags (myapp:1.0, myapp:latest)
- Stored in registries

### 3. Dockerfile
Text file with instructions to build an image. Commands include:
- FROM: Base image
- RUN: Execute commands during build
- COPY: Copy files from host to container
- WORKDIR: Set working directory
- EXPOSE: Document exposed ports
- ENV: Set environment variables
- CMD: Default command to run
- ENTRYPOINT: Container entry point

### 4. Registry
Repository storing Docker images:
- Docker Hub (public)
- Private registries (AWS ECR, Google Container Registry, Azure ACR)

### 5. Layer
Docker images built in layers:
- Each Dockerfile instruction creates a layer
- Layers are cached for faster builds
- Only changed layers are rebuilt

---

## Docker Architecture

### Key Components:

1. **Docker Daemon (dockerd)**: Background process managing containers
2. **Docker Client**: CLI tool for user interaction
3. **Docker Images**: Read-only templates
4. **Docker Containers**: Runtime instances
5. **Docker Registries**: Image storage and distribution

Flow: User → Docker Client → Docker Daemon → Container Management

---

## Basic Docker Commands

### Image Commands:
```bash
docker build -t myimage:1.0 .         # Build image from Dockerfile
docker pull ubuntu:20.04              # Pull image from registry
docker push myrepo/myimage:1.0        # Push image to registry
docker images                          # List local images
docker rmi image_id                   # Remove image
docker tag source_image target_image  # Tag an image
docker inspect image_name             # Get image details
```

### Container Commands:
```bash
docker run -d -p 8080:80 nginx            # Run container in background
docker ps                                  # List running containers
docker ps -a                               # List all containers
docker stop container_id                   # Stop container
docker start container_id                  # Start stopped container
docker restart container_id                # Restart container
docker rm container_id                     # Remove container
docker logs container_id                   # View container logs
docker exec -it container_id bash         # Execute command in container
docker attach container_id                 # Attach to container
```

### Useful Options:
- `-d`: Detached mode (background)
- `-it`: Interactive and terminal
- `-p`: Port mapping (host:container)
- `-v`: Volume mounting
- `-e`: Environment variables
- `--name`: Container name
- `--network`: Network mode

---

## Docker Images

### Creating Images

**Dockerfile Example:**
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

**Build Command:**
```bash
docker build -t myapp:1.0 .
```

### Image Best Practices:
- Use specific base image versions
- Minimize layer count by combining commands
- Use multi-stage builds to reduce image size
- Exclude unnecessary files with .dockerignore
- Don't run containers as root
- Keep images small and focused

---

## Docker Containers

### Container Lifecycle:
1. **Created**: Container created from image
2. **Running**: Container is executing
3. **Paused**: Container temporarily paused
4. **Stopped**: Container stopped but not removed
5. **Removed**: Container deleted

### Container Port Mapping:
```bash
docker run -p 8080:80 nginx  # Host port 8080 → Container port 80
docker run -P nginx          # Random port assignment
```

### Environment Variables:
```bash
docker run -e VAR_NAME=value myimage
docker run --env-file env.txt myimage
```

---

## Docker Networking

### Network Types:

1. **Bridge** (default): Containers connected via virtual bridge
2. **Host**: Container uses host network
3. **None**: Container isolated from network
4. **Overlay**: For Docker Swarm

### Network Commands:
```bash
docker network ls              # List networks
docker network create mynet    # Create custom network
docker network connect mynet container_id  # Connect container to network
docker network inspect mynet   # View network details
```

---

## Docker Volumes

### Volume Types:

1. **Named Volumes**: Managed by Docker
```bash
docker volume create myvolume
docker run -v myvolume:/data myimage
```

2. **Bind Mounts**: Mount host directory
```bash
docker run -v /host/path:/container/path myimage
```

3. **tmpfs Mounts**: Temporary in-memory storage
```bash
docker run --tmpfs /app/temp myimage
```

### Volume Commands:
```bash
docker volume ls
docker volume inspect myvolume
docker volume rm myvolume
```

---

## Docker Compose

### What is Docker Compose?
Tool for defining and running multi-container applications using YAML.

### Example docker-compose.yml:
```yaml
version: '3.8'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
  
  db:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

### Common Commands:
```bash
docker-compose up              # Start services
docker-compose up -d           # Start in background
docker-compose down            # Stop and remove services
docker-compose ps              # List services
docker-compose logs            # View service logs
docker-compose build           # Build images
```

---

## Best Practices

### Image Best Practices:
- Use specific base image versions, not 'latest'
- Minimize layers and combine commands where possible
- Use multi-stage builds for smaller final images
- Exclude unnecessary files with .dockerignore
- Don't run containers as root user
- Keep images small and focused
- Use official base images
- Scan images for vulnerabilities

### Container Best Practices:
- One process per container
- Use environment variables for configuration
- Implement proper logging
- Handle signals gracefully (SIGTERM, SIGKILL)
- Set resource limits (CPU, memory)
- Use health checks
- Don't store data in containers
- Use meaningful container names

### Security Best Practices:
- Run containers as non-root user
- Use read-only filesystems where possible
- Limit container capabilities
- Use network policies
- Scan images for vulnerabilities
- Keep images updated
- Use secrets for sensitive data
- Implement proper access controls

### DevOps Best Practices:
- Version your images
- Use CI/CD pipelines for building/pushing
- Implement proper logging and monitoring
- Use orchestration platforms (Kubernetes) for production
- Implement proper backup strategies
- Document Dockerfiles and architecture
- Use private registries for proprietary images
- Implement automated testing

---

## Docker in DevOps

Docker is essential for modern DevOps practices:
- **Containerization**: Standardize application packaging
- **CI/CD Integration**: Automate build, test, and deployment
- **Microservices**: Enable scalable architectures
- **Infrastructure as Code**: Reproduce environments easily
- **Orchestration**: Use with Kubernetes for production

---

## Common Use Cases

1. **Development Environment**: Consistent dev environment across teams
2. **Testing**: Isolated test environments
3. **Staging**: Pre-production environment
4. **Production Deployment**: Scalable application deployment
5. **Continuous Integration**: Automated testing pipelines
6. **Microservices**: Independent service deployment
7. **Machine Learning**: Reproducible ML pipelines

---

## Conclusion

Docker revolutionized application development and deployment by providing:
- Consistency across environments
- Simplified dependency management
- Efficient resource utilization
- Rapid deployment capabilities
- Foundation for DevOps and cloud-native practices

Mastering Docker is essential for modern software engineers and DevOps professionals working with cloud infrastructure and containerization technologies.
