# Making Containers Immutable

## Introduction

Immutable containers are a crucial aspect of modern DevOps practices, enhancing security, consistency, and reliability in containerized environments. This guide explores techniques to make containers immutable, focusing on Kubernetes-specific approaches and best practices.

## What are Immutable Containers?

Immutable containers are containers that, once created, cannot be modified during runtime. This approach ensures consistency across environments and reduces security risks associated with runtime changes.

## Techniques for Making Containers Immutable

### 1. Set readOnlyFileSystem to true

Using Kubernetes security contexts, you can make the container's file system read-only.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readonly-pod
spec:
  containers:
  - name: app
    image: your-image:tag
    securityContext:
      readOnlyRootFilesystem: true
```

#### Handling Errors

After setting `readOnlyRootFilesystem: true`, you may encounter errors. To resolve these:

1. Check container logs:
   ```
   kubectl logs <pod-name>
   ```

2. Identify required writable directories.

3. Mount volumes for necessary write access:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readonly-pod
spec:
  containers:
  - name: app
    image: your-image:tag
    securityContext:
      readOnlyRootFilesystem: true
    volumeMounts:
    - name: tmp
      mountPath: /tmp
    - name: var-run
      mountPath: /var/run
  volumes:
  - name: tmp
    emptyDir: {}
  - name: var-run
    emptyDir: {}
```

### 2. Set privileged flag to false

Ensure containers don't have elevated privileges:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: non-privileged-pod
spec:
  containers:
  - name: app
    image: your-image:tag
    securityContext:
      privileged: false
```

### 3. Run as Non-Root User

Avoid running containers as root:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: non-root-pod
spec:
  containers:
  - name: app
    image: your-image:tag
    securityContext:
      runAsUser: 1000
      runAsGroup: 3000
```

## Best Practices

1. Use minimal base images (e.g., Alpine Linux).
2. Remove unnecessary tools and packages from the container.
3. Implement proper logging mechanisms for debugging.
4. Use multi-stage builds to reduce image size and attack surface.
5. Regularly update and patch your container images.

## Common Pitfalls

1. Overlooking application-specific write requirements.
2. Assuming all containers can run as non-root without modifications.
3. Neglecting to test thoroughly after implementing immutability.
4. Forgetting to update CI/CD pipelines to accommodate immutable containers.

## Example: Troubleshooting Apache with ReadOnlyRootFilesystem

When using `readOnlyRootFilesystem: true` with an Apache container, you may encounter errors like:

```
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.6. Set the 'ServerName' directive globally to suppress this message
[Sun Jan 05 08:40:04.540278 2025] [core:error] [pid 1:tid 1] (30)Read-only file system: AH00099: could not create /usr/local/apache2/logs/httpd.pid.H7Le2S
[Sun Jan 05 08:40:04.540322 2025] [core:error] [pid 1:tid 1] AH00100: httpd: could not log pid to file /usr/local/apache2/logs/httpd.pid
```

To resolve this, you need to mount volumes for all directories that Apache needs to write to:

```yaml
spec:
  containers:
  - image: httpd
    name: triton
    securityContext:
      readOnlyRootFilesystem: true
    volumeMounts:
    - mountPath: /usr/local/apache2/logs
      name: log-volume
    - mountPath: /usr/local/apache2/run
      name: run-volume
  volumes:
  - emptyDir: {}
    name: log-volume
  - emptyDir: {}
    name: run-volume
```

This configuration mounts separate volumes for both the logs and run directories, allowing Apache to write to these locations while keeping the rest of the filesystem read-only.

### Why mounting just '/usr/local' doesn't solve the problem

When trying to make the Apache container work with a read-only filesystem, you might think that mounting '/usr/local' as writable would solve all issues. However, this approach doesn't work as expected. Here's why:

1. File system hierarchy: In the Apache container, the directory structure looks like this:

   /usr/local/apache2/
   ├── bin/
   ├── cgi-bin/
   ├── conf/
   ├── error/
   ├── htdocs/
   ├── icons/
   ├── logs/  <-- Needs to be writable
   ├── modules/
   └── run/   <-- Needs to be writable

   Only the 'logs' and 'run' directories need to be writable for Apache to function correctly.

2. Read-only root filesystem behavior: When you set `readOnlyRootFilesystem: true`, Kubernetes mounts the container's root filesystem as read-only before applying any volume mounts.

3. Volume mount behavior: When you mount a volume, it overlays the existing content of the mount point in the container.

Let's look at what happens with different approaches:

Example 1: Mounting '/usr/local' as writable

```yaml
volumeMounts:
- mountPath: /usr/local
  name: usr-local-volume
```

This would make '/usr/local' writable, but it would also replace all existing content in '/usr/local' with an empty directory. Apache would fail to start because all its files (configurations, modules, etc.) would be missing.

Example 2: Mounting '/usr/local/apache2'

```yaml
volumeMounts:
- mountPath: /usr/local/apache2
  name: apache-volume
```

This has the same problem as Example 1, but at the apache2 directory level. All Apache files would be replaced with an empty directory.

Example 3: Mounting only required directories (correct approach)

```yaml
volumeMounts:
- mountPath: /usr/local/apache2/logs
  name: log-volume
- mountPath: /usr/local/apache2/run
  name: run-volume
```
This approach only overlays the specific directories that need to be writable, leaving the rest of the Apache installation intact and read-only.

By mounting volumes only to '/usr/local/apache2/logs' and '/usr/local/apache2/run', we provide the necessary write access without disturbing the rest of the Apache installation. This maintains the benefits of an immutable container while allowing Apache to function correctly.

## Further Reading

1. [Kubernetes Security Context Documentation](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
2. [Docker Security Best Practices](https://docs.docker.com/develop/security-best-practices/)
3. [CNCF Blog: Principles of Container-based Application Design](https://www.cncf.io/blog/2017/11/28/12-fractured-apps-12-factor-app-cloud-native-microservices-world/)
4. [Red Hat: Immutable Infrastructure: Foundation for Your Cloud-Native Journey](https://www.redhat.com/en/blog/immutable-infrastructure-foundation-your-cloud-native-journey)
5. [Apache HTTP Server Project Documentation](https://httpd.apache.org/docs/)
