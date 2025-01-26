### Pointers
- *Continuous Integration / Continuous Deployment* -  "continuous" emphasizes the frequent and seamless integration and delivery of software updates. The process relies on automation to handle building, testing, and deploying code efficiently. It is designed to be repeatable and to maintain high speed, ensuring rapid feedback and deployment.
- *Cloud Native Computing Foundation*
- According to Kubernetes' official deprecation policy, stable API elements (those that are Generally Available (GA)) **must be supported for at least 12 months after being deprecated**. This ensures that users have enough time to transition to newer API versions or features before the deprecated element is removed.
- A **StatefulSet requires a headless service** for stable network identities and **DNS resolution**. This is critical for workloads needing consistent pod identities, such as databases or distributed systems.
- OnDelete: When a StatefulSet's .spec.updateStrategy.type is set to OnDelete, the StatefulSet controller will not automatically update the Pods in a StatefulSet.
- **GitOps tools like Argo CD and Flux** use Git as the single source of truth for application deployments. They continuously compare the desired state in Git with the actual state in the cluster, ensuring that any discrepancies are either automatically corrected or flagged for review. This process promotes consistency and automation in deployment.
-  GitOps uses Git as the single source of truth for deployment and cluster configuration. This approach ensures consistent and reliable deployments, minimizes configuration drift (differences between the desired state and the actual state), and eliminates fragile release processes by automating and streamlining them.
- Application message distribution: This refers to communication systems like **RabbitMQ or Kafka**, which are unrelated to the scheduling and management of containers.
- ExternalName maps a service to an external DNS name and requires explicit configuration.
- [Rook](https://rook.io/) is a Kubernetes operator designed for managing storage systems like Ceph. It provides features like self-scaling, self-healing, and dynamic provisioning, making it ideal for automating storage management within Kubernetes.
- OCI is responsible for developing and maintaining open industry standards around container formats and runtimes. **The OCI defines specifications like the container image format and the runtime specifications**, ensuring interoperability across container ecosystems.
- To ensure optimal performance of an etcd cluster, network throughput and disk I/O are the most critical resources. **etcd requires high network throughput for fast data replication between cluster members and high disk I/O to handle its logging and data storage operations**. This ensures the consistency and responsiveness of the key-value store.
- **Dockershim was a CRI-compliant adapter** that allowed Docker to be used as a container runtime with Kubernetes. While it is deprecated, it was CRI-compatible.
- The Kubernetes scheduler primarily uses Pod resource requests (like memory), node taints (to prevent Pods from being scheduled on inappropriate nodes), and Pod affinity (to manage relationships between Pods) to make scheduling decisions. Labels are useful for selection, but the core factors involve resources, taints, and affinity.
- A Kubernetes Service can expose multiple ports. Each port should have a unique port number & unique name: 
- The governance board in an open source project is primarily responsible for establishing and maintaining the project's strategic direction, decision-making processes, and the rules for community interaction (the terms of engagement). They ensure the project operates smoothly, adheres to its mission, and has a clear structure for contributions and growth.
- In Kubernetes, **Services and Pods are REST objects** because they are managed and manipulated through the Kubernetes API, which follows RESTful principles. Although they can be represented in formats like JSON or YAML, the underlying interaction with the system is based on the REST API.
- Cloud-native applications are designed to embrace modern development principles, including: Resiliency, Agility, Operability, Observability (ORAO)
-  set up a highly available Kubernetes cluster - cluster with at least three nodes (to form a quorum) [3 CP + 3 ETCD]
- Zookeeper is great for systems that need strong coordination and reliability, particularly in very large and complex environments. On the other hand, etcd is simple to set up, uses fewer resources, and performs faster, making it a good choice for smaller systems or those that need to scale easily.
- When a PersistentVolume (PV) has a Retain reclaim policy, the volume is not automatically deleted or recycled after the corresponding PersistentVolumeClaim (PVC) is deleted. Instead, the volume remains in the cluster, and manual steps are required to reclaim or clean up the data. This policy is useful for retaining critical data even after the PVC is deleted.
- Distributed tracing is implemented at the application layer in cloud-native environments. It provides visibility into the flow of requests across services, helping with performance monitoring and debugging by tracking service interactions.
- The correct semantic versioning format follows the structure MAJOR.MINOR.PATCH, and can optionally include pre-release labels like -rc for release candidates. Semantic versioning allows software to indicate the level of change
-  CloudEvents is an open standard for event data, providing a consistent way to describe events that can be used across different cloud providers, services, and platforms. The goal is to improve interoperability, making it easier to process and manage events in various systems.
-  Kubernetes Operators are designed to extend Kubernetes' functionality to manage complex, stateful applications. Operators use custom controllers to automate tasks like deployment, scaling, and recovery of stateful applications (e.g., databases, message queues), handling their lifecycle and maintaining their desired state.
-  A PersistentVolume (PV) is a piece of storage in the Kubernetes cluster that is provisioned by an administrator.
  
### Popular Kubernetes distributions and their specific use cases:

- **K3s** - K3s is a lightweight Kubernetes distribution tailored for edge computing and resource-constrained environments. It is optimized for minimal overhead and reduced resource usage, making it ideal for distributed systems at the edge.
- **Minikube** - is for local single-node cluster testing.
- **EKS/GKE/AKS/OpenShift (Red Hat)** - Production level
- **Kind (Kubernetes in Docker)** - is used for development and testing clusters in Docker, not edge scenarios.
- **MicroK8s (Canonical)** - focuses on simplicity for local development but is not edge-specific.

    
### [High-Level View of DevOps, SRE, Cloud, and Platform Roles](https://www.splunk.com/en_us/blog/learn/sre-vs-devops-vs-platform-engineering.html)

**DevOps**
DevOps teams work in collaboration to automate and streamline the software development process. Also, DevOps greatly benefits teams by improving collaboration, communication, software delivery speed and quality. Responsibilities of DevOps engineers include:
- Prioritizing communication and collaboration.
- Automating processes.
- System monitoring.
- Optimizing system performance.
- Troubleshooting issues.

**SRE (Site Reliability Engineer)**
Site reliability engineering (SRE) is a practice that focuses on improving and maintaining the reliability of a software system. It utilizes software tools and automated tasks like application monitoring and reliability tasks to accomplish that.

**Cloud Engineer**
Cloud Engineer roles are like architects of the cloud. They specialize in setting up and managing cloud infrastructure, making sure it's efficient, secure, and cost-effective. They use tools like AWS or Azure to create environments where applications can thrive.

**Platform Engineer**
Platform Engineer roles are like builders of developer-friendly platforms. They design and maintain systems that empower developers to manage their applications easily, from setting up workflows to monitoring performance. It's all about creating a smooth experience for everyone involved.

- While SRE teams mainly focus on improving the reliability of a software system,
- DevOps teams streamline software development and deployment through close collaboration with operations teams.
- On the other hand, platform engineering teams facilitate infrastructure by providing toolchains and workflows required for the development teams.

- Automation and monitoring are common tasks for all three roles, using similar tools.

**FinOps (Financial Operations)** is the practice of bringing financial accountability to the cloud by enabling organizations to manage their cloud spend effectively. It aims to optimize cloud costs and integrate financial considerations into development and operational workflows.

**FaaS(Function as a service (FaaS))**: This refers to serverless computing 

### container runtime
runC is the official container runtime that implements the OCI Runtime Specification, making it the correct choice when looking for an OCI-compliant native runtime.

While other runtimes such as runV, Kata Containers, and gVisor are also OCI-compliant, they are alternative runtimes that provide additional features, such as enhanced security or isolation, but they do not serve as the "native" runtime for container environments like runC does.
- runV is focused on providing virtual machine-based isolation for containers
- Kata Containers also uses lightweight virtual machines to offer strong isolation
- gVisor adds a layer of security by intercepting system calls to provide additional isolation
- cri-o: Focused on Kubernetes but not as broadly used as containerd.
- lxd: Focused on system containers, not typical application containers.
- kata-runtime: Focused on security isolation with lightweight virtual machines.

### Observability
- Logs: Captures events/messages but not the end-to-end request flow
- Traces are used to represent the flow of requests through a distributed system, capturing the relationships between individual services or components
- Spans are part of a trace, but traces themselves provide a complete view of the request's journey through the system
- Metrics: Provides numerical data but not the flow of requests across services
  - Summary: Aggregates data over time, providing percentiles and counts, but not for fluctuating values
  - Counter: Only increases and is not suitable for metrics that decrease
  - Histogram: Used for distributions, not single fluctuating values
  - A Gauge is the metric type used in Prometheus to track a value that can fluctuate, increasing and decreasing over time. It’s ideal for metrics such as system resource usage, dynamic statuses, or other values that may go up and down during operation
- In PromQL, metrics are queried by their names followed by a set of label filters inside curly braces {}. This syntax allows you to filter data based on the values of labels such as job, instance, etc. (e.g - http_requests_total{job="apiserver"})
- Prometheus is a popular monitoring and alerting toolkit, and the prometheus-adapter allows Kubernetes Horizontal Pod Autoscalers (HPA) to use custom metrics collected by Prometheus.

  
 ### Tools:
- **GitOps Toolkit**: This term refers to a set of tools and libraries, not a specific tool for syncing with Git repositories.
- **Linkerd and Istio**: These are service mesh tools, not tools for syncing Kubernetes clusters with Git repositories.
- **Helm and Kustomize**: These tools are used for resource management and customization, but they do not automate the GitOps process for syncing clusters with repositories.
- **Falc0** is an open-source runtime security tool designed to detect anomalous behavior and security threats in containerized environments. It monitors system calls at runtime and can identify unexpected activities, such as unauthorized access or suspicious file changes, making it a widely used tool for Kubernetes and container runtime security.
- **Rancher Kubernetes Engine (RKE)** -  Multi-cluster management


### probes    
A probe in Kubernetes is a diagnostic tool that the kubelet uses to assess the health and readiness of containers. Kubernetes provides three types of probes:
- Startup Probe: Ensures the container has started successfully. If it fails, the container is terminated
- Readiness Probe: Checks if the container is ready to serve traffic. If it fails, the container is removed from the service endpoints
- Liveness Probe: Ensures the container is still running. If it fails, the container is restarted

Probes are crucial for maintaining the health and reliability of Kubernetes applications.

### Service Mesh:
The Service Mesh Interface (SMI) is the common standard for service meshes. It provides a set of APIs that allow different service mesh implementations to interoperate in a Kubernetes environment. 

A service mesh is designed to handle complex microservices architectures, especially those with thousands of distributed services spread across multiple clusters. It helps manage service-to-service communication, provides observability, traffic management, and security features like encryption and authentication across the services, which is crucial in such large and distributed environments.

The correct terminology for a service mesh involves the data plane (which handles the traffic between services) and the control plane (which manages the configuration).  A service mesh typically includes a control plane to manage configurations and a service proxy to handle traffic between services, ensuring observability, security, and reliability
- Service proxy: Handles communication between services within the mesh and provides features such as load balancing, traffic routing, and service discovery.
- Control plane: Manages the configuration and policies that control the behavior of the proxies and the overall service mesh.

***Linkerd*** is a lightweight service mesh that manages traffic flows between services, enforces access policies, and collects telemetry data, all without requiring changes to application code. It operates by injecting a proxy alongside the application’s container, providing features like load balancing, service discovery, and security.

### Headless Service 
Headless Service is a service that is explicitly configured without a ClusterIP. This is done by setting the clusterIP field to None in the service's definition. 
- Without a ClusterIP, Kubernetes does not create a virtual IP for the service.
- Instead, DNS records are created that resolve directly to the IP addresses of the individual pods matching the service's selector.
- Headless services are commonly used for stateful applications (e.g., StatefulSets) or for custom service discovery mechanisms where you need direct access to the pods rather than a load-balanced virtual IP.

### Scheduling:
The kube-scheduler performs two main steps to select a node for scheduling a Pod:
- Filtering: The scheduler filters out nodes that do not meet the requirements for running the Pod, such as resource availability (CPU, memory), taints, affinity rules, etc.
- Scoring: Once the candidate nodes are filtered, the scheduler scores the remaining nodes to determine the best one based on various factors, such as resource utilization, proximity to other Pods, or specific performance metrics.

### Open Policy Agent (OPA) 
is a general-purpose policy engine that can be used for policy enforcement across various systems, including Kubernetes. In Kubernetes, OPA can be integrated with admission controllers to validate incoming requests and enforce policies on resources like Pods, Services, and Deployments.

Some common use cases for OPA in Kubernetes include:
- Validating resource requests: Ensuring that resources like CPU or memory requests for containers comply with specified limits.
- Enforcing security policies: Ensuring that labels, annotations, or network policies are applied correctly.
- Complying with organizational policies: Verifying compliance with organizational standards for naming conventions, configurations, etc.
- OPA policies are written in Rego

### Linux Capabilities 
- Security profiles, such as SELinux or AppArmor, define broader security settings that are enforced by the control plane to apply those security contexts.
- In Kubernetes, Security Contexts are configurations applied to Pods and containers at runtime to define specific security settings such as user IDs, groups, and capabilities. 
  - Restricted: Containers must drop ALL capabilities, and are only permitted to add back the NET_BIND_SERVICE capability
  - Baseline:
  ```
    AUDIT_WRITE
    CHOWN
    DAC_OVERRIDE
    FOWNER
    FSETID
    KILL
    MKNOD
    NET_BIND_SERVICE
    SETFCAP
    SETGID
    SETPCAP
    SETUID
    SYS_CHROOT
    ```
  - Privileged: ALL

