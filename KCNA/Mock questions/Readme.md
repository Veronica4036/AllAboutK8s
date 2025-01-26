### Pointers
- Continuous Integration / Continuous Deployment
- According to Kubernetes' official deprecation policy, stable API elements (those that are Generally Available (GA)) must be supported for at least 12 months after being deprecated. This ensures that users have enough time to transition to newer API versions or features before the deprecated element is removed.
- A StatefulSet requires a headless service for stable network identities and DNS resolution. This is critical for workloads needing consistent pod identities, such as databases or distributed systems.
- GitOps tools like Argo CD and Flux use Git as the single source of truth for application deployments. They continuously compare the desired state in Git with the actual state in the cluster, ensuring that any discrepancies are either automatically corrected or flagged for review. This process promotes consistency and automation in deployment.
- Application message distribution: This refers to communication systems like RabbitMQ or Kafka, which are unrelated to the scheduling and management of containers.
- ExternalName maps a service to an external DNS name and requires explicit configuration.
- [Rook](https://rook.io/) is a Kubernetes operator designed for managing storage systems like Ceph. It provides features like self-scaling, self-healing, and dynamic provisioning, making it ideal for automating storage management within Kubernetes.

### Popular Kubernetes distributions and their specific use cases:

1. **K3s**
- Use case: IoT, Edge computing, ARM devices
- Features: Lightweight, single binary (<100MB), low resource usage
- Perfect for: Raspberry Pi, edge locations, development environments

2. **Minikube**
- Use case: Local development and testing
- Features: Single-node cluster, runs in VM
- Perfect for: Developers learning K8s, testing applications locally

3. **EKS (Amazon Elastic Kubernetes Service)**
- Use case: Production workloads on AWS
- Features: Managed K8s, deep AWS integration
- Perfect for: Enterprise applications on AWS, hybrid cloud setups

4. **GKE (Google Kubernetes Engine)**
- Use case: Production workloads on Google Cloud
- Features: Auto-pilot mode, deep GCP integration
- Perfect for: Cloud-native applications, ML/AI workloads

5. **AKS (Azure Kubernetes Service)**
- Use case: Production workloads on Azure
- Features: Managed K8s, Azure integration
- Perfect for: Enterprise applications on Azure, .NET workloads

6. **OpenShift (Red Hat)**
- Use case: Enterprise container platform
- Features: Additional security, developer tools, GUI
- Perfect for: Large enterprises, hybrid cloud deployments

7. **Rancher Kubernetes Engine (RKE)**
- Use case: Multi-cluster management
- Features: Cluster management UI, multi-cloud support
- Perfect for: Organizations managing multiple K8s clusters

8. **Kind (Kubernetes in Docker)**
- Use case: Testing and CI/CD
- Features: Runs K8s clusters in Docker containers
- Perfect for: CI/CD pipelines, quick testing

9. **MicroK8s (Canonical)**
- Use case: Small-scale production, IoT
- Features: Low overhead, single-node deployment
- Perfect for: Small deployments, edge computing, IoT

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

- Application monitoring
- Emergency response
- Change management
- Ensuring the availability, efficiency and performance standards of applications

**Cloud Engineer**
Cloud Engineer roles are like architects of the cloud. They specialize in setting up and managing cloud infrastructure, making sure it's efficient, secure, and cost-effective. They use tools like AWS or Azure to create environments where applications can thrive.

**Platform Engineer**
Platform Engineer roles are like builders of developer-friendly platforms. They design and maintain systems that empower developers to manage their applications easily, from setting up workflows to monitoring performance. It's all about creating a smooth experience for everyone involved.

- While SRE teams mainly focus on improving the reliability of a software system,
- DevOps teams streamline software development and deployment through close collaboration with operations teams.
- On the other hand, platform engineering teams facilitate infrastructure by providing toolchains and workflows required for the development teams.

- Automation and monitoring are common tasks for all three roles, using similar tools.

### container runtime
runC is the official container runtime that implements the OCI Runtime Specification, making it the correct choice when looking for an OCI-compliant native runtime.

While other runtimes such as runV, Kata Containers, and gVisor are also OCI-compliant, they are alternative runtimes that provide additional features, such as enhanced security or isolation, but they do not serve as the "native" runtime for container environments like runC does.
- runV is focused on providing virtual machine-based isolation for containers
- Kata Containers also uses lightweight virtual machines to offer strong isolation
- gVisor adds a layer of security by intercepting system calls to provide additional isolation

### probes    
A probe in Kubernetes is a diagnostic tool that the kubelet uses to assess the health and readiness of containers. Kubernetes provides three types of probes:
- Startup Probe: Ensures the container has started successfully. If it fails, the container is terminated
- Readiness Probe: Checks if the container is ready to serve traffic. If it fails, the container is removed from the service endpoints
- Liveness Probe: Ensures the container is still running. If it fails, the container is restarted

Probes are crucial for maintaining the health and reliability of Kubernetes applications.

### Service Mesh:
The Service Mesh Interface (SMI) is the common standard for service meshes. It provides a set of APIs that allow different service mesh implementations to interoperate in a Kubernetes environment.

The correct terminology for a service mesh involves the data plane (which handles the traffic between services) and the control plane (which manages the configuration).  A service mesh typically includes a control plane to manage configurations and a service proxy to handle traffic between services, ensuring observability, security, and reliability
- Service proxy: Handles communication between services within the mesh and provides features such as load balancing, traffic routing, and service discovery.
- Control plane: Manages the configuration and policies that control the behavior of the proxies and the overall service mesh.

### Headless Service 
Headless Service is a service that is explicitly configured without a ClusterIP. This is done by setting the clusterIP field to None in the service's definition. 
- Without a ClusterIP, Kubernetes does not create a virtual IP for the service.
- Instead, DNS records are created that resolve directly to the IP addresses of the individual pods matching the service's selector.
- Headless services are commonly used for stateful applications (e.g., StatefulSets) or for custom service discovery mechanisms where you need direct access to the pods rather than a load-balanced virtual IP.

### Open Policy Agent (OPA) 
is a general-purpose policy engine that can be used for policy enforcement across various systems, including Kubernetes. In Kubernetes, OPA can be integrated with admission controllers to validate incoming requests and enforce policies on resources like Pods, Services, and Deployments.

Some common use cases for OPA in Kubernetes include:
- Validating resource requests: Ensuring that resources like CPU or memory requests for containers comply with specified limits.
- Enforcing security policies: Ensuring that labels, annotations, or network policies are applied correctly.
- Complying with organizational policies: Verifying compliance with organizational standards for naming conventions, configurations, etc.
- OPA policies are written in Rego
- 
