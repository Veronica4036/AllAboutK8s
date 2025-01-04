# KubeLinter and Kubesec: Kubernetes Manifest Analysis Tools

## Introduction

KubeLinter and Kubesec are powerful tools for analyzing Kubernetes manifests to ensure they follow best practices and security guidelines. This document provides an overview of how to use these tools and what to look for when reviewing Kubernetes manifests.

## KubeLinter

KubeLinter is an open-source tool that checks Kubernetes YAML files and Helm charts for potential issues.

### Usage

To analyze a manifest with KubeLinter:

```bash
kube-linter lint <manifest-file>
```

Example:
```bash
kube-linter lint nginx.yml
```

### Key Areas to Check

1. **Pod Anti-Affinity**: Ensure replicas are scheduled on different nodes.
2. **Read-Only Root Filesystem**: Prevent unauthorized modifications to the container's filesystem.
3. **Non-Root User**: Run containers as non-root users for improved security.
4. **Resource Requests and Limits**: Set appropriate CPU and memory constraints.
5. **Security Context**: Configure security settings for pods and containers.
6. **Network Policies**: Define and enforce network access rules.
7. **Image Pull Policies**: Ensure proper image versioning and pulling strategies.
8. **Liveness and Readiness Probes**: Configure health checks for containers.
9. **Service Account Usage**: Use appropriate service accounts with least privileges.
10. **Secret Management**: Properly handle and reference secrets.

### Example Output and Remediation

```
KubeLinter 0.7.1

/root/nginx.yml: (object: <no namespace>/nginx-deployment apps/v1, Kind=Deployment) container "nginx" does not have a read-only root file system (check: no-read-only-root-fs, remediation: Set readOnlyRootFilesystem to true in the container securityContext.)
```

Remediation:
```yaml
spec:
  template:
    spec:
      containers:
      - name: nginx
        securityContext:
          readOnlyRootFilesystem: true
```

## Kubesec

Kubesec is a security risk analysis tool for Kubernetes resources.

### Usage

To scan a manifest with Kubesec and save the report:

```bash
kubesec scan <manifest-file> > <output-file>
```

Example:
```bash
kubesec scan /root/node.yaml > /root/kubesec_report.json
```

### Key Areas to Check

1. **Privileged Containers**: Avoid using privileged containers.
2. **Service Account Configuration**: Use least-privilege service accounts.
3. **AppArmor Profiles**: Implement AppArmor policies for enhanced security.
4. **Seccomp Profiles**: Use Seccomp to restrict system calls.
5. **Resource Limits**: Set CPU and memory limits to prevent resource exhaustion.
6. **Capability Dropping**: Reduce kernel capabilities to minimize attack surface.
7. **Read-Only Root Filesystem**: Implement immutable root filesystems.
8. **Non-Root User Execution**: Run containers as non-root users.
9. **High UID Usage**: Use high UIDs to avoid conflicts with host user tables.
10. **Security Context**: Configure appropriate security contexts for pods and containers.

### Example Output and Interpretation

```json
{
  "object": "Pod/node.default",
  "valid": true,
  "fileName": "/root/node.yaml",
  "message": "Failed with a score of -27 points",
  "score": -27,
  "scoring": {
    "critical": [
      {
        "id": "Privileged",
        "selector": "containers[] .securityContext .privileged == true",
        "reason": "Privileged containers can allow almost completely unrestricted host access",
        "points": -30
      }
    ],
    // ... (other sections omitted for brevity)
  }
}
```

In this example, the manifest fails due to the use of privileged containers, which is a critical security risk.

## Best Practices

1. Run KubeLinter and Kubesec as part of your CI/CD pipeline.
2. Address all critical and high-severity issues before deployment.
3. Regularly update and review your Kubernetes manifests.
4. Use the principle of least privilege when configuring pods and containers.
5. Implement network policies to control traffic between pods.
6. Use version-pinned container images to ensure consistency.
7. Implement proper secret management using Kubernetes Secrets or external secret management tools.

## Common Pitfalls

1. Ignoring security warnings from linting tools.
2. Using privileged containers unnecessarily.
3. Failing to set resource limits, leading to potential DoS scenarios.
4. Running containers as root when not required.
5. Neglecting to implement network policies, leaving pods exposed.
6. Using latest tags for container images instead of specific versions.
7. Hardcoding secrets in manifests instead of using proper secret management.

## Further Reading

1. [Kubernetes Security Best Practices](https://kubernetes.io/docs/concepts/security/overview/)
2. [KubeLinter GitHub Repository](https://github.com/stackrox/kube-linter)
3. [Kubesec Documentation](https://kubesec.io/)
4. [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes)
5. [Kubernetes Goat - Vulnerable Cluster Environment](https://github.com/madhuakula/kubernetes-goat)
