# Understanding `pidof`, System Calls, Seccomp & App Armor in Kubernetes

## 1. Using `pidof`

```bash
# Find PID of a specific Nginx worker process
$ pidof nginx | cut -d' ' -f1
12345

# Kill all instances of a process
$ kill $(pidof firefox)
```

## 2. System Calls and Common Commands

```bash
# Trace system calls of a running process
$ strace -p $(pidof nginx | cut -d' ' -f1)

# Trace a command and summarize system call usage
$ strace -c ls /etc

# Trace specific system calls for a command
$ strace -e trace=open,read,write echo "Hello, World!"

# Trace child processes as well
$ strace -f python3 my_script.py
```

## 3. Understanding Seccomp

Seccomp (secure computing mode) restricts the syscalls a process can make.

### How Seccomp Works:
1. Process starts with all syscalls allowed.
2. Seccomp mode is enabled.
3. Only specified syscalls are permitted.
4. Blocked syscall attempts result in process termination.

### Seccomp Modes:
1. Strict Mode: Allows only `read`, `write`, `exit`, and `sigreturn`.
2. Filter Mode: Uses BPF rules to specify allowed syscalls.

## 4. Seccomp Profiles in Kubernetes

Kubernetes supports three types of seccomp profiles:

1. **RuntimeDefault**: Uses the container runtime's default seccomp profile.
2. **Localhost**: Uses a custom seccomp profile on the node.
3. **Unconfined**: Disables seccomp (not recommended for production).

### 4.1 Applying Seccomp Profiles

#### RuntimeDefault Profile:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: audit-pod
spec:
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  containers:
  - name: test-container
    image: hashicorp/http-echo:0.2.3
```

#### Localhost Profile:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: audit-pod
spec:
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/custom-seccomp.json
  containers:
  - name: test-container
    image: hashicorp/http-echo:0.2.3
```

#### Unconfined Profile (not recommended):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: audit-pod
spec:
  securityContext:
    seccompProfile:
      type: Unconfined
  containers:
  - name: test-container
    image: hashicorp/http-echo:0.2.3
```

### 4.2 Storage and Management of Seccomp Profiles

Custom seccomp profiles are typically stored on the Kubernetes nodes. The default location is:

```
/var/lib/kubelet/seccomp/
```

To use a custom profile:

1. Create your JSON profile (e.g., `custom-seccomp.json`).
2. Place it in the `/var/lib/kubelet/seccomp/` directory on each node where the pod might run.
3. Reference it in your pod specification using the relative path.

Example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/custom-seccomp.json
  containers:
  - name: secure-container
    image: nginx:latest
```

In this example, the full path of the profile on the node would be:
```
/var/lib/kubelet/seccomp/profiles/custom-seccomp.json
```

Note:
- Ensure the profile is present on all nodes where the pod might be scheduled.
- Consider using a configuration management tool or DaemonSet to distribute and update profiles across your cluster.
- The kubelet must be configured to use this directory (it's the default, but could be changed in some configurations).


### 4.3 Whitelist Profile Example

```json
{
    "defaultAction": "SCMP_ACT_ERRNO",
    "architectures": ["SCMP_ARCH_X86_64"],
    "syscalls": [
        {
            "names": [
                "read",
                "write",
                "open"
                // ... other allowed syscalls
            ],
            "action": "SCMP_ACT_ALLOW"
        }
    ]
}
```

### 4.4 Blacklist Profile Example

```json
{
    "defaultAction": "SCMP_ACT_ALLOW",
    "architectures": ["SCMP_ARCH_X86_64"],
    "syscalls": [
        {
            "names": [
                "mount",
                "reboot",
                "ptrace"
                // ... other blocked syscalls
            ],
            "action": "SCMP_ACT_ERRNO"
        }
    ]
}
```

## AppArmor and Security Context in Kubernetes

AppArmor is a Linux kernel security module that provides Mandatory Access Control (MAC) for applications. It allows administrators to restrict the capabilities of individual programs, enhancing system security. This document covers AppArmor usage in Kubernetes, related commands, and comparison with other security features.

## AppArmor Basics

### Checking AppArmor Status

To check if AppArmor is loaded and get profile information:

```bash
aa-status
```

This command provides details on:
- Whether AppArmor is loaded
- Total number of loaded profiles
- Profiles in enforce mode
- Profiles in complain mode

### Common AppArmor Commands

1. `aa-enforce`: Set a profile to enforce mode
2. `aa-complain`: Set a profile to complain mode
3. `aa-disable`: Disable a profile
4. `aa-unconfined`: List processes with no AppArmor profile
5. `apparmor_parser`: Load or reload AppArmor profiles

### AppArmor Modes

- **Enforce Mode**: Actively prevents profile violations
- **Complain Mode**: Logs violations but doesn't prevent them
- **Disabled**: Profile is not applied

## AppArmor in Kubernetes

### Loading a Custom Profile

To load a custom AppArmor profile:

```bash
sudo apparmor_parser -r -v /etc/apparmor.d/usr.sbin.nginx
```

### Applying AppArmor to Kubernetes Pods

Use annotations to apply AppArmor profiles to pods:

```yaml
metadata:
  annotations:
    container.apparmor.security.beta.kubernetes.io/nginx: localhost/custom-nginx
```

### Using SecurityContext

Apply AppArmor profiles using `securityContext`:

```yaml
securityContext:
  appArmorProfile:
    type: Localhost
    localhostProfile: k8s-apparmor-example-deny-write
```

## Checking Linux Capabilities of Containers

### Method 1: Using `docker inspect`

```bash
docker inspect --format '{{.State.Pid}}' <container_id>
cat /proc/<container_pid>/status | grep Cap
```

### Method 2: Using `capsh`

```bash
docker exec <container_id> capsh --print
```

### Method 3: Using `getpcaps`

```bash
getpcaps <container_pid>
```

## AppArmor vs. Seccomp

| Feature | AppArmor | Seccomp |
|---------|----------|---------|
| Focus | File system access, network operations | System calls |
| Granularity | Application-specific | System-wide or per-process |
| Configuration | Profile-based | Syscall filter lists |
| Kubernetes Integration | Native support | Supported via pod security context |


## Further Reading

1. [Kubernetes AppArmor Documentation](https://kubernetes.io/docs/tutorials/security/apparmor/)
2. [Ubuntu AppArmor Documentation](https://ubuntu.com/server/docs/security-apparmor)
3. [AppArmor Wiki](https://gitlab.com/apparmor/apparmor/-/wikis/home)
4. [Linux Capabilities Overview](https://man7.org/linux/man-pages/man7/capabilities.7.html)
5. [Seccomp in Kubernetes](https://kubernetes.io/docs/tutorials/security/seccomp/)
