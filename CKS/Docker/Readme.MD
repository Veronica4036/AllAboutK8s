# Docker Basics: Essential Commands and Best Practices

## Introduction

Docker is a platform for developing, shipping, and running applications in containers. This guide covers essential Docker commands, best practices, and common pitfalls to help you effectively work with Docker containers and images.

## Table of Contents

1. [Basic Docker Commands](#basic-docker-commands)
2. [Working with Containers](#working-with-containers)
3. [Dockerfile Best Practices](#dockerfile-best-practices)
4. [Security Considerations](#security-considerations)
5. [Common Pitfalls](#common-pitfalls)
6. [Further Reading](#further-reading)

## Basic Docker Commands

### Listing Containers

```bash
docker ps -a
```

This command lists all containers, including stopped ones. The `-a` flag shows all containers, while `docker ps` alone shows only running containers.

### Executing Commands in Containers

```bash
docker exec <container_name> <command>
```

This allows you to run a command inside a running container. For example:

```bash
docker exec my_container ls -l /app
```

### Removing Containers

```bash
docker rm <container_name> --force
```

The `--force` flag stops the container if it's running before removing it.

### Running Containers

```bash
docker run --name=<name> <image_name> <command>
```

This command creates and starts a new container. For example:

```bash
docker run --name my_nginx -d nginx:alpine
```

### Sharing PID Namespace

```bash
docker run --name app2 --pid=container:app1 -d nginx:alpine sleep infinity
```

This command runs a container that shares the PID namespace with another container (`app1`).

### Inspecting Images

```bash
docker inspect <image>
```

This provides detailed information about a Docker image, including its layers, configuration, and metadata.

## Working with Containers

### Building Images

To build an image from a Dockerfile:

```bash
docker build -t <image_name> <path_to_dockerfile>
```

Example:

```bash
docker build -t my_app:v1 .
```

### Running Containers with Environment Variables

```bash
docker run -e VAR_NAME=value -d <image_name>
```

This is useful for passing secrets or configuration at runtime.

### Accessing Container Logs

```bash
docker logs <container_name>
```

This command displays the logs of a running container.

## Dockerfile Best Practices

1. **Use Specific Base Image Versions**: Always specify exact versions of base images to ensure reproducibility.

   ```dockerfile
   FROM ubuntu:20.04
   ```

2. **Minimize Layers**: Combine RUN commands to reduce the number of layers and image size.

   ```dockerfile
   RUN apt-get update && apt-get install -y \
       package1 \
       package2 \
       && rm -rf /var/lib/apt/lists/*
   ```

3. **Use .dockerignore**: Create a `.dockerignore` file to exclude unnecessary files from the build context.

4. **Don't Run as Root**: Use the `USER` instruction to run the container as a non-root user.

   ```dockerfile
   RUN useradd -m myuser
   USER myuser
   ```

5. **Use Multi-stage Builds**: For compiled languages, use multi-stage builds to keep final images small.

   ```dockerfile
   # Build stage
   FROM golang:1.16 AS builder
   WORKDIR /app
   COPY . .
   RUN go build -o myapp

   # Final stage
   FROM alpine:3.14
   COPY --from=builder /app/myapp /usr/local/bin/
   CMD ["myapp"]
   ```

6. **Layer Optimization**: Order your Dockerfile instructions to maximize layer caching and minimize image size.

   ```dockerfile
   COPY package.json package-lock.json ./
   RUN npm ci
   COPY . .
   RUN npm run build
   ```

7. **Leverage BuildKit**: Enable Docker BuildKit for more efficient, concurrent, and cache-intelligent builds.

   ```bash
   DOCKER_BUILDKIT=1 docker build -t myimage .
   ```

## Security Considerations

1. **Remove Unnecessary Tools**: In production images, remove debugging tools and shells if not needed.

   ```dockerfile
   RUN apt-get update && apt-get install -y myapp \
       && rm -rf /var/lib/apt/lists/* \
       && rm -rf /bin/sh
   ```

2. **Use Secrets Management**: Avoid hardcoding secrets in Dockerfiles. Use runtime environment variables or secrets management tools.

3. **Scan Images**: Regularly scan your Docker images for vulnerabilities.

4. **Limit Capabilities**: Use `--cap-drop` to remove unnecessary Linux capabilities from containers.

## Common Pitfalls

1. **Ignoring Build Context**: Large build contexts can slow down builds. Use `.dockerignore` effectively.

2. **Not Cleaning Up**: Always remove unnecessary files after installation to keep images small.

3. **Using Latest Tag**: Avoid using the `latest` tag in production as it's mutable and can lead to inconsistencies.

4. **Ignoring Layer Caching**: Organize Dockerfile instructions to maximize cache usage for faster builds.

5. **Running as Root**: Running containers as root can pose security risks. Use `USER` instruction to switch to a non-root user.

## Further Reading

1. [Docker Documentation](https://docs.docker.com/)
2. [Docker Security Best Practices](https://snyk.io/blog/10-docker-image-security-best-practices/)
3. [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
4. [Docker Networking Guide](https://docs.docker.com/network/)
5. [Docker Compose for Multi-Container Applications](https://docs.docker.com/compose/)

This comprehensive guide covers the basics of Docker, including essential commands, best practices for writing Dockerfiles, security considerations, and common pitfalls to avoid. By following these guidelines, you can create more efficient, secure, and maintainable Docker-based applications.
