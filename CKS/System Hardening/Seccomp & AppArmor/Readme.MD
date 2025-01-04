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

## 5. Best Practices

- Start with the default Docker seccomp profile and customize.
- Use `strace` to identify necessary syscalls.
- Test profiles in non-production environments first.
- Regularly review and update profiles.

## 6. Troubleshooting

If a container fails to start:
1. Check container logs for seccomp-related errors.
2. Use `strace` to identify blocked syscalls.
3. Temporarily disable the seccomp profile to confirm it's the cause.
4. Gradually add necessary syscalls to the profile.
