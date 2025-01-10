# Certified Kubernetes Security Specialist (CKS) Exam Cheat Sheet

## Introduction

This cheat sheet covers key topics and commands for the Certified Kubernetes Security Specialist (CKS) exam. It's designed as a quick reference guide for exam preparation and last-minute review.

## Cluster Setup and Hardening

### API Server Security

```bash
# Enable audit logging
--audit-log-path=/var/log/kubernetes/audit.log
--audit-policy-file=/etc/kubernetes/audit-policy.yaml

# Enable admission controllers
--enable-admission-plugins=NodeRestriction,PodSecurityPolicy
```

### ETCD Encryption

```bash
# Generate encryption key
ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)

# Create encryption config
cat > encryption-config.yaml <<EOF
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: ${ENCRYPTION_KEY}
      - identity: {}
EOF
```

### Cluster Upgrade

```bash
# Upgrade kubeadm
apt-get update && apt-get install -y kubeadm=1.xx.x-00

# Plan the upgrade
kubeadm upgrade plan

# Apply the upgrade
kubeadm upgrade apply v1.xx.x

# Upgrade kubelet and kubectl
apt-get update && apt-get install -y kubelet=1.xx.x-00 kubectl=1.xx.x-00
```

## System Hardening

### Minimize Host OS Footprint

```bash
# Remove unnecessary packages
apt-get remove --purge <package-name>

# Disable unused services
systemctl disable <service-name>
```

### Restrict Network Access

```bash
# Configure firewall rules
ufw allow 6443/tcp
ufw allow 2379:2380/tcp
ufw enable
```

## Minimize Microservice Vulnerabilities

### Pod Security Policies

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  runAsUser:
    rule: MustRunAsNonRoot
  fsGroup:
    rule: RunAsAny
  volumes:
  - 'configMap'
  - 'emptyDir'
  - 'projected'
  - 'secret'
  - 'downwardAPI'
  - 'persistentVolumeClaim'
```

### Network Policies

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

## Supply Chain Security

### Image Scanning

```bash
# Scan image with Trivy
trivy image <image-name>
```

### Secure Supply Chain

```yaml
# Example of using a digest instead of a tag
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  containers:
  - name: secure-container
    image: nginx@sha256:abcd1234...
```

## Monitoring, Logging, and Runtime Security

### Falco

```bash
# Install Falco
curl -s https://falco.org/repo/falcosecurity-3672BA8F.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" | tee -a /etc/apt/sources.list.d/falcosecurity.list
apt-get update -y
apt-get install -y falco

# Start Falco
systemctl start falco
```

### Audit Logging

```yaml
# Audit policy example
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
  resources:
  - group: ""
    resources: ["pods"]
```

## Cluster Hardening

### RBAC

```yaml
# Role example
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

# RoleBinding example
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Secure Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - secure.example.com
    secretName: tls-secret
  rules:
  - host: secure.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: secure-service
            port: 
              number: 80
```

## Further Reading

1. [Kubernetes Documentation - Security](https://kubernetes.io/docs/concepts/security/)
2. [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes)
3. [Kubernetes Security Best Practices](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)
4. [Falco Documentation](https://falco.org/docs/)
5. [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
