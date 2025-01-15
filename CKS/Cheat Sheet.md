Part 1: Generic Docker/Linux Commands
=====================================

## Docker Basic

1. docker ps -a (list all containers)
2. docker exec <container name> <command>
3. docker rm <container name> —force
4. docker run --name=<name> <image name> <command>
5. docker run --name app2 --pid=container:app1 -d nginx:alpine sleep infinity
6. docker inspect <image>


## DevOps: Optimizing Dockerfiles for Security and Efficiency

### Introduction

This guide focuses on optimizing a Dockerfile for security and efficiency. We'll cover best practices for building Docker images, including version specificity, layer caching, secret management, and container security.

### Main Content

#### 1. Base Image Version Specificity

Always use specific versions for base images to ensure reproducibility and consistency.

```dockerfile
FROM ubuntu:20.04
```

#### 2. Optimizing Layer Caching

Combine related commands to reduce the number of layers and optimize caching.

```dockerfile
RUN apt-get update && apt-get install -y curl
```

#### 3. Secret Management

Use environment variables to pass secrets at runtime instead of hardcoding them in the Dockerfile.

```dockerfile
ENV URL https://google.com/this-will-fail?secret-token=
CMD ["sh", "-c", "curl --head $URL$TOKEN"]
```

#### 4. Preventing Exec Access

Remove unnecessary shells or use minimal base images to enhance security.

```dockerfile
RUN rm /usr/bin/bash
```

### Complete Optimized Dockerfile

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
ENV URL https://google.com/this-will-fail?secret-token=
RUN rm /usr/bin/bash
CMD ["sh", "-c", "curl --head $URL$TOKEN"]
```

### Building and Running the Container

```bash
# Build the image
podman build -t app .

# Run the container with a secret token
podman run -e TOKEN=2e064aad-3a90-4cde-ad86-16fad1f8943e app
```

## Linux Commands

Unzip Tar file:
```
tar -xvf file.tar
```

Send file from CP to node:
```
scp /root/profile node01:/root
```

Replace text:
```
tr " " "\n"
```

Check ownership of a directory:
```
ls -lh /var/lib | grep etcd
```

View file permissions:
```
stat -c %a /var/lib/etcd
```

Binary checks:
```
sha512sum <binary>
cat compare | uniq
```

Grep usage:
```
grep containerLog /var/lib/kubelet/config.yaml
```

Search for specific line across multiple files:
```
grep "search_term" *
grep "search_term" *.txt
grep -r "search_term" .
grep -n "search_term" *
grep -i "search_term" *
```

Part 2: CKS-Specific Information
================================

## Cluster Setup - 10%

### CIS Benchmarks for Control Plane:
```
kube-bench | grep <test-case>       ❌
kube-bench --check="1.2.21,1.2.22"  ✅
```

Run all the checks under this comma-delimited list of groups. Example --group="1.2"
```
controlplane $ kube-bench --group="1.2"       
[INFO] 1 Master Node Security Configuration
[INFO] 1.2 API Server
[WARN] 1.2.1 Ensure that the --anonymous-auth argument is set to false (Manual)
[PASS] 1.2.2 Ensure that the --token-auth-file parameter is not set (Automated)
```


 
### Verify Platform Binaries:
```
sha512sum kubernetes/server/bin/kubelet
sha512sum /usr/bin/kubelet
```

For control plane components:
```
crictl ps | grep <component>
ps aux | grep <component>
find /proc/<pid>/root/ | grep <component>
sha512sum /proc/<pid>/root/usr/local/bin/<component>
```

## Cluster Hardening - 15%

### RBAC:
```
Role → RoleBinding ✅
Role → ClusterRoleBinding ❌
ClusterRole → RoleBinding ✅
ClusterRole → ClusterRoleBinding ✅
```

### Important locations:
Kubelet config:
```
/var/lib/kubelet/config.yaml
```

## System Hardening - 15%

### AppArmor:
Check existing profiles:
```
ssh node01
apparmor_status
```

Add profile:
```
apparmor_parser <path to profile>
```

## Minimize Microservice Vulnerabilities - 20%

### Cilium Network Policies:
(Example policies provided in the original cheat sheet)

## Supply Chain Security - 20%

### OpenSSL Basics:
Generate private key:
```
openssl genrsa -out <key_name.key> 2048
```

Create Certificate Signing Request (CSR):
```
openssl req -new -key <key_generated_above> -out <csr_name.csr>
```

Decode CSR content:
```
cat <csr_name.csr> | base64 -w 0
```

## Monitoring, Logging and Runtime Security - 20%

 How to find falco rules like a pro:
```
controlplane $ cd /etc/falco/
controlplane $ ls
aws_cloudtrail_rules.yaml  falco.yaml  falco_rules.local.yaml  falco_rules.yaml  k8s_audit_rules.yaml  rules.available  rules.d
 grep -n "A shell was spawned in a" *.yaml
falco_rules.yaml:2024:    A shell was spawned in a container with an attached terminal (user=%user.name user_loginuid=%user.loginuid %container.info
controlplane $ vi +2024 falco_rules.yaml
```


# from MOCK

## SBOM/TRIVY
 bom generate --image registry.k8s.io/kube-apiserver:v1.31.0 --format json --output /opt/course/18/sbom1.json

➜ candidate@cks8930:~$ trivy image --help | grep format
  $ trivy image --format json --output result.json alpine:3.15
  # Generate a report in the CycloneDX format
  $ trivy image --format cyclonedx --output result.cdx alpine:3.15
  -f, --format string              format (table,json,template,sarif,cyclonedx,spdx,spdx-json,github,cosign-vuln) (default "table")
...

➜ candidate@cks8930:~$ trivy image --format cyclonedx --output /opt/course/18/sbom2.json registry.k8s.io/kube-controller-manager:v1.31.0


## AUDITING

# cks3477:/etc/kubernetes/audit/policy.yaml
apiVersion: audit.k8s.io/v1
kind: Policy
rules:

# log Secret resources audits, level Metadata
- level: Metadata
  resources:
  - group: ""
    resources: ["secrets"]

# log node related audits, level RequestResponse
- level: RequestResponse
  userGroups: ["system:nodes"]

# for everything else don't log anything 
- level: None

- 
