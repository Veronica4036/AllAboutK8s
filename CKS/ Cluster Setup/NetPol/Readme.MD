# Kubernetes NetworkPolicy Logic: AND/OR Operations

## Introduction

Kubernetes NetworkPolicies are crucial for controlling traffic flow within a cluster. Understanding the logic behind these policies, particularly how AND and OR operations work, is essential for implementing effective network security. This guide explores various scenarios, provides examples, and offers best practices for working with NetworkPolicies.

## NetworkPolicy Logic Explained

In Kubernetes NetworkPolicies:
- Items within a single `to` or `from` block use AND logic.
- Separate `to` or `from` blocks use OR logic.
- Separate rules in `egress` or `ingress` arrays use OR logic.

## Scenarios and Examples

### 1. AND Logic (within a single block)

```yaml
egress:
- to:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: space2
    podSelector:
      matchLabels:
        app: web
    ports:
    - protocol: TCP
      port: 80
```

This policy allows traffic to pods that meet ALL of these criteria:
- In namespace "space2"
- Labeled "app: web"
- Only on TCP port 80

### 2. OR Logic (separate blocks)

```yaml
egress:
- to:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: space2
- to:
  - podSelector:
      matchLabels:
        app: database
```

This policy allows traffic to EITHER:
- Any pod in namespace "space2"
- Any pod labeled "app: database" in the policy's namespace

### 3. Combination of AND and OR

```yaml
egress:
- to:
  - namespaceSelector:
      matchLabels:
        environment: production
    podSelector:
      matchLabels:
        app: web
- to:
  - namespaceSelector:
      matchLabels:
        environment: staging
  ports:
  - protocol: TCP
    port: 80
```

This policy allows traffic to EITHER:
- Pods in production namespace AND labeled "app: web"
- Any pod in staging namespace, but only on TCP port 80

### 4. Multiple Rules (OR logic between rules)

```yaml
egress:
- to:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: space2
- ports:
  - protocol: UDP
    port: 53
  - protocol: TCP
    port: 53
```

This policy allows EITHER:
- Traffic to any pod in namespace "space2" on any port
- DNS traffic (TCP/UDP port 53) to any namespace

### 5. Common Mistake (unintended OR logic)

```yaml
egress:
- to:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: space2
  - podSelector:
      matchLabels:
        app: web
```

This policy actually allows traffic to EITHER:
- Any pod in namespace "space2"
- Any pod labeled "app: web" in the policy's namespace

Many users mistakenly believe this creates an AND condition, but it's actually OR due to the YAML structure.

## Specifying Ports in Ingress and Egress

### Example: Allow SQL, App1, and DNS

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-sql-app1-dns
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: app1
    ports:
    - protocol: TCP
      port: 3306
  egress:
  - ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53
```

This policy:
- Allows incoming SQL traffic (port 3306) from pods labeled "app: app1"
- Allows outgoing DNS traffic (UDP and TCP port 53) to any destination

## Best Practices

1. Use specific selectors: Avoid overly broad policies.
2. Implement the principle of least privilege.
3. Test policies thoroughly before applying to production.
4. Use `kubectl describe networkpolicy` to verify interpreted rules.
5. Combine multiple small policies instead of creating complex ones.

## Common Pitfalls

1. Misunderstanding AND/OR logic in YAML structure.
2. Overlooking the default deny-all behavior when any policy is applied.
3. Forgetting to allow necessary system traffic (e.g., DNS, metrics).
4. Not considering the impact on existing communications when applying new policies.

## Further Reading

1. [Kubernetes NetworkPolicy Documentation](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
2. [Network Policy Recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes)
3. [Calico Network Policy](https://docs.projectcalico.org/security/network-policy)
4. [Kubernetes Network Policies: A Practical Guide](https://medium.com/@reuvenharrison/kubernetes-network-policies-a-practical-guide-9a3cd54034a4)
5. [NetworkPolicy Editor](https://editor.cilium.io/) - Visual tool for creating and understanding NetworkPolicies

By understanding these concepts and following best practices, you can create effective and secure NetworkPolicies in your Kubernetes clusters.
