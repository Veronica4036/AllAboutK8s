### Container & Orchestration History
```markdown
- Docker was originally called DotCloud (PaaS company founded in 2008 by Solomon Hykes, Kamel Founadi and Sebastien Pahl)
- Kubernetes was announced by Google on June 6, 2014
- According to Kubernetes' deprecation policy, stable API elements must be supported for at least 12 months after deprecation
```

### Container Runtimes & Standards
```markdown
- runC: Official OCI-compliant native container runtime
- Alternative runtimes:
  - runV: VM-based isolation
  - Kata Containers: Lightweight VM isolation
  - gVisor: Security through system call interception
  - cri-o: Kubernetes-focused
  - lxd: System containers
  - kata-runtime: Security isolation

- OCI defines:
  - Container image format specifications
  - Runtime specifications
  - Ensures container ecosystem interoperability

- Dockershim was a CRI-compliant adapter for Docker with Kubernetes (now deprecated)
```

### Linux Foundations for Containers
```markdown
Linux Namespaces (2002):
- Mount (mnt): File system mount points
- PID (pid): Process IDs
- IPC (ipc): Interprocess communication
- Network (net): Network resources
- User (user): User/group IDs
- UTS: Hostname/domain settings

Linux Capabilities:
Security Profiles:
1. Restricted: Must drop ALL, only NET_BIND_SERVICE allowed
2. Baseline: Allows:
   - AUDIT_WRITE, CHOWN, DAC_OVERRIDE
   - FOWNER, FSETID, KILL
   - MKNOD, NET_BIND_SERVICE
   - SETFCAP, SETGID, SETPCAP
   - SETUID, SYS_CHROOT
3. Privileged: ALL capabilities
```

### Kubernetes Distributions
```markdown
1. K3s: 
   - Edge computing
   - IoT
   - Resource-constrained environments

2. Production-Grade:
   - EKS (AWS)
   - GKE (Google Cloud)
   - AKS (Azure)
   - OpenShift (Red Hat)

3. Development/Testing:
   - Minikube: Local single-node
   - Kind: Development in Docker
   - MicroK8s: Simple local development
```

### Kubernetes Components & Concepts
```markdown
1. StatefulSet:
   - Requires headless service
   - Needs stable network identities
   - DNS resolution
   - UpdateStrategy.type OnDelete: No automatic pod updates

2. Services:
   - Can expose multiple ports (unique numbers & names)
   - Services and Pods are REST objects
   - ExternalName maps to external DNS
   - Headless Service: 
     - ClusterIP set to None
     - Direct DNS to pod IPs
     - Used with StatefulSets

3. Storage:
   - PersistentVolume (PV): Admin-provisioned storage
   - Retain policy: Manual cleanup after PVC deletion
   - Rook: Storage system operator for Ceph

4. Scheduling:
   - Two main steps:
     1. Filtering: Remove unsuitable nodes
     2. Scoring: Rank remaining nodes
   - Uses: Pod resources, node taints, Pod affinity

5. Node Controller:
   - Monitors node health
   - Handles Pod rescheduling on failures
```

### Cloud Native Concepts
```markdown
1. Cloud Native Principles (ORAO):
   - Resiliency
   - Agility
   - Operability
   - Observability

2. High Availability:
   - Minimum 3 nodes for quorum
   - 3 Control Plane + 3 ETCD nodes

3. Data Storage & Coordination:
   - etcd: Needs high network throughput & disk I/O
   - Zookeeper: Strong coordination for large systems
   - etcd: Simpler, faster for smaller systems
```

### DevOps & Related Roles
```markdown
1. DevOps:
   - Focus: Process automation & streamlining
   - Key tasks: System monitoring, optimization, troubleshooting
   - Emphasis on collaboration

2. SRE (Site Reliability Engineer):
   - Focus: System reliability
   - Uses automation & monitoring
   - Maintains system stability

3. Cloud Engineer:
   - Focus: Cloud infrastructure
   - Manages cloud environments
   - Ensures efficiency & security

4. Platform Engineer:
   - Focus: Developer platforms
   - Creates workflows & toolchains
   - Improves developer experience

5. FinOps:
   - Focus: Cloud cost management
   - Financial accountability
   - Optimize cloud spending
```

### Observability Tools & Concepts
```markdown
1. Metrics Types:
   - Gauge: Fluctuating values
   - Counter: Only increasing values
   - Histogram: Value distributions
   - Summary: Time-based aggregations

2. Monitoring Components:
   - Logs: Event/message capture
   - Traces: End-to-end request flow
   - Spans: Parts of a trace
   - Metrics: Numerical data

3. Tools:
   - Prometheus: Monitoring & alerting
   - prometheus-adapter: HPA custom metrics
   - PromQL syntax: metrics{label="value"}
```

### Security & Policy Tools
```markdown
1. Open Policy Agent (OPA):
   - Policy engine
   - Uses Rego language
   - Use cases:
     - Resource validation
     - Security policy enforcement
     - Organizational compliance

2. Security Tools:
   - Falco: Runtime security
   - Security Contexts: Pod/container security settings
```

### GitOps & CI/CD
```markdown
1. GitOps Concepts:
   - Git as single source of truth
   - Automated state reconciliation
   - Configuration drift prevention

2. Tools:
   - GitOps Toolkit: Flux foundation
   - Flux & Argo CD: Main GitOps tools
   - Not GitOps specific:
     - Helm
     - Kustomize
     - Jenkins
     - GitLab CI/CD

3. CI/CD Principles:
   - Continuous integration
   - Continuous deployment
   - Automated testing & deployment
```

### Service Mesh & Networking
```markdown
1. Service Mesh Components:
   - Data plane: Traffic handling
   - Control plane: Configuration management
   - Service proxy: Load balancing & routing

2. Key Aspects:
   - SMI: Service Mesh Interface standard
   - Handles distributed architectures
   - Provides observability & security

3. Tools:
   - Linkerd: Lightweight service mesh
   - Istio: Full-featured service mesh
```

### Container & Application Health
```markdown
Kubernetes Probes:
1. Startup Probe:
   - Initial container startup
   - Failed = container termination

2. Readiness Probe:
   - Traffic serving readiness
   - Failed = removed from service

3. Liveness Probe:
   - Container running status
   - Failed = container restart
```

### Versioning & Standards
```markdown
1. Semantic Versioning:
   - Format: MAJOR.MINOR.PATCH
   - Optional: -rc for release candidates

2. CloudEvents:
   - Open standard for event data
   - Cross-platform compatibility
   - Improves interoperability
```
