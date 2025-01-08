Certainly! I'll break down the cheat sheet into two parts as requested: Generic Docker/Linux commands and CKS-specific information. I'll also organize the CKS-specific information according to the sections you've linked.

Part 1: Generic Docker/Linux Commands
=====================================

## Docker Basic

1. docker ps -a (list all containers)
2. docker exec <container name> <command>
3. docker rm <container name> —force
4. docker run --name=<name> <image name> <command>
5. docker run --name app2 --pid=container:app1 -d nginx:alpine sleep infinity
6. docker inspect <image>

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
kube-bench | grep <test-case>
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

(No specific commands provided in the original cheat sheet for this section)

Remember to refer to the official CKS exam guide and practice with hands-on labs to fully prepare for each section of the exam.
