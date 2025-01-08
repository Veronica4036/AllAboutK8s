# Docker Basics: Essential Commands and Best Practices

## Introduction

Docker is a platform for developing, shipping, and running applications in containers. This guide covers essential Docker commands, best practices, and common pitfalls to help you effectively work with Docker containers and images, with detailed explanations for beginners.

## Table of Contents

1. [Basic Docker Commands](#basic-docker-commands)
2. [Working with Containers](#working-with-containers)
3. [Dockerfile Best Practices](#dockerfile-best-practices)
4. [Security Considerations](#security-considerations)
5. [Common Pitfalls](#common-pitfalls)
6. [Further Reading](#further-reading)

Thank you for providing this detailed interaction. It's an excellent example of how Docker's auto-completion works in practice. Let's use this to create a more accurate and comprehensive guide on using Docker's auto-completion feature:

# Docker Command Line Tips: Interactive Help and Auto-completion

Docker's command-line interface offers powerful auto-completion features that can significantly enhance your productivity and accuracy when working with Docker commands. Here's a step-by-step guide on how to use it effectively:

## Using Auto-completion for Command Options

1. Start your Docker command:
   ```
   docker run -d --name app2
   ```

2. When you reach an option like `--pid`, type it and press Tab:
   ```
   controlplane $ docker run -d --name app2 --pid[TAB]
   --pid         --pids-limit  
   ```
   This shows you the available options starting with `--pid`.

3. If you choose `--pid` and press Tab again:
   ```
   controlplane $ docker run -d --name app2 --pid[TAB]
   container:  host        
   ```
   This displays the two main options for the `--pid` flag.

4. If you choose `container:` and press Tab:
   ```
   controlplane $ docker run -d --name app2 --pid container:[TAB]
   app1   app2   apps   sapp1  
   ```
   This shows a list of available container names you can connect to.

5. Complete your command by selecting a container and adding the image name:
   ```
   controlplane $ docker run -d --name app2 --pid container:sapp1 nginx
   9a797ba835a2ead40e054d9fe444f4767e0e7166097d15a4cad3e3fab1106f37
   ```

## Key Points to Remember

1. Double-tabbing after an option often reveals more specific choices.
2. The auto-completion can show you:
   - Available flag options (like `--pid` vs `--pids-limit`)
   - Specific choices for a flag (like `container:` or `host` for `--pid`)
   - Existing resources (like container names when using `--pid container:`)
3. Auto-completion helps prevent typos and syntax errors.
4. It can also remind you of available options you might have forgotten.

## Practical Tips

- Use Tab completion frequently to explore available options.
- If you're unsure about a command's syntax, start typing and use Tab to see what's available.
- Remember that auto-completion can show you container names, image names, and more, depending on the context.

By leveraging these auto-completion features, you can construct Docker commands more quickly and with fewer errors, even when dealing with complex options or when you're not entirely familiar with all available choices.

## Basic Docker Commands

### Listing Containers

```bash
docker ps -a
```

- `docker ps`: Lists running containers.
- `-a`: Shows all containers, including stopped ones.
- Without `-a`, you'd only see currently running containers.

### Executing Commands in Containers

```bash
docker exec <container_name> <command>
```

- Runs a command inside a running container.
- `<container_name>`: The name or ID of your container.
- `<command>`: The command you want to run inside the container.

Example:
```bash
docker exec my_container ls -l /app
```
This runs `ls -l /app` inside a container named `my_container`, listing the contents of the `/app` directory.

### Removing Containers

```bash
docker rm <container_name> --force
```

- Removes a container.
- `<container_name>`: The name or ID of the container to remove.
- `--force`: Stops the container if it's running, then removes it.

### Running Containers

```bash
docker run --name=<name> <image_name> <command>
```

- Creates and starts a new container from an image.
- `--name=<name>`: Assigns a name to the new container.
- `<image_name>`: The name of the Docker image to use.
- `<command>`: (Optional) A command to run in the container.

Example:
```bash
docker run --name my_nginx -d nginx:alpine
```
Creates a container named `my_nginx` from the `nginx:alpine` image, running in detached mode (`-d`).

### Sharing PID Namespace

```bash
docker run --name app2 --pid=container:app1 -d nginx:alpine sleep infinity
```

- Runs a new container that shares the Process ID (PID) namespace with another container.
- `--name app2`: Names the new container `app2`.
- `--pid=container:app1`: Shares the PID namespace with a container named `app1`.
- `-d`: Runs in detached mode.
- `nginx:alpine`: The image to use.
- `sleep infinity`: A command to keep the container running indefinitely.

### Inspecting Images

```bash
docker inspect <image>
```

- Provides detailed information about a Docker image.
- `<image>`: The name or ID of the image you want to inspect.

## Working with Containers

### Building Images

```bash
docker build -t <image_name> <path_to_dockerfile>
```

- Builds a Docker image from a Dockerfile.
- `-t <image_name>`: Tags the image with a name.
- `<path_to_dockerfile>`: The directory containing the Dockerfile.

Example:
```bash
docker build -t my_app:v1 .
```
Builds an image named `my_app` with tag `v1` using the Dockerfile in the current directory.

### Running Containers with Environment Variables

```bash
docker run -e VAR_NAME=value -d <image_name>
```

- Runs a container with environment variables.
- `-e VAR_NAME=value`: Sets an environment variable inside the container.
- `-d`: Runs in detached mode.
- `<image_name>`: The image to use for the container.

### Accessing Container Logs

```bash
docker logs <container_name>
```

- Displays the logs of a running container.
- `<container_name>`: The name or ID of the container.

## Dockerfile Best Practices

1. **Use Specific Base Image Versions**:
   ```dockerfile
   FROM ubuntu:20.04
   ```
   - `FROM`: Sets the base image for your Docker image.
   - `ubuntu:20.04`: Specifies the exact version of Ubuntu to use, ensuring consistency.

2. **Minimize Layers**:
   ```dockerfile
   RUN apt-get update && apt-get install -y \
       package1 \
       package2 \
       && rm -rf /var/lib/apt/lists/*
   ```
   - `RUN`: Executes commands in a new layer.
   - Chaining commands with `&&` creates a single layer instead of multiple layers.
   - Cleaning up (`rm -rf /var/lib/apt/lists/*`) reduces image size.

3. **Use .dockerignore**: 
   - Specifies which files and directories should be excluded from the Docker build context, similar to .gitignore.

4. **Don't Run as Root**:
   ```dockerfile
   RUN useradd -m myuser
   USER myuser
   ```
   - Creates a new user and switches to it for subsequent instructions and container runtime.

5. **Use Multi-stage Builds**:
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
   - Uses two stages: a build environment and a minimal runtime environment.
   - `WORKDIR`: Sets the working directory inside the container.
   - `COPY`: Copies files from the build context to the container.
   - `RUN`: Compiles the application.
   - `COPY --from=builder`: Copies the compiled application from the build stage to the final stage.
   - `CMD`: Specifies the command to run when the container starts.

6. **Layer Optimization**:
   ```dockerfile
   COPY package.json package-lock.json ./
   RUN npm ci
   COPY . .
   RUN npm run build
   ```
   - Optimizes layer caching by copying and installing dependencies before copying the rest of the application code.

7. **Leverage BuildKit**:
   ```bash
   DOCKER_BUILDKIT=1 docker build -t myimage .
   ```
   - Enables BuildKit, Docker's new build system, for faster and more efficient builds.

## Security Considerations

1. **Remove Unnecessary Tools**:
   ```dockerfile
   RUN apt-get update && apt-get install -y myapp \
       && rm -rf /var/lib/apt/lists/* \
       && rm -rf /bin/sh
   ```
   - Installs only necessary packages and removes package lists and shell to reduce attack surface.

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

This comprehensive guide covers the basics of Docker, including essential commands with detailed explanations, best practices for writing Dockerfiles, security considerations, and common pitfalls to avoid. By following these guidelines, you can create more efficient, secure, and maintainable Docker-based applications, even as a beginner to Docker.
