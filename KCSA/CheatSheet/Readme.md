## Kubernetes Threat Model

Scenario: An attacker has gained access to a container in your Kubernetes cluster and is attempting to maintain long-term access.What could the attacker exploit to achieve persistence?

Correct answer (RBAC permissions): RBAC (Role-Based Access Control) permissions can be exploited by an attacker to maintain persistence within a Kubernetes cluster. By escalating privileges or misusing permissions, the attacker can access various resources and maintain control over the cluster. Network policies, read-only filesystems, and immutable containers are defensive measures that can limit an attacker's capabilities.

RBAC permissions

Scenario: A logistics company is concerned about SQL injection attacks on their backend API services.What best practice should they adopt to mitigate this risk?

Correct answer (Using prepared statements and parameterized queries): Using prepared statements and parameterized queries is the most effective way to prevent SQL injection attacks. This approach ensures that user input is treated as data rather than executable code, effectively mitigating the risk of injection. Relying solely on input validation or static code analysis may not be sufficient, and applying server-side validation without parameterized queries does not fully prevent SQL injections.

Using prepared statements and parameterized queries

Scenario: An online retailer needs to prevent unauthorized access to Kubernetes management components.What is the best measure they should implement?

Correct answer (Binding etcd to localhost): Binding etcd to localhost limits access to etcd, the Kubernetes data store, ensuring that it cannot be accessed externally. This reduces the attack surface and prevents unauthorized entities from interacting with critical cluster data. Configuring minimal logging, using firewall rules, or implementing mutual TLS are useful practices, but restricting etcd access directly addresses unauthorized access to management components.

Binding etcd to localhost

Scenario: A Kubernetes cluster is vulnerable to attacks due to outdated container images.Which action can reduce this vulnerability?

Correct answer (Regularly updating container images): Regularly updating container images ensures that security patches and updates are applied, reducing the risk of vulnerabilities being exploited. Keeping images up-to-date is a fundamental practice in maintaining a secure Kubernetes environment. While enforcing image scanning and using signed images are also important, regular updates are directly effective in addressing vulnerabilities.

Regularly updating container images

Scenario: You need to detect and respond to unusual network activities in your Kubernetes cluster. What best approach should you take?

Correct answer (Setting up monitoring with Prometheus): Setting up monitoring with Prometheus allows for comprehensive monitoring of network activities and system metrics, enabling the detection of unusual patterns and rapid response to potential threats. Prometheus can collect and analyze metrics in real-time, providing alerts for anomalous behavior. Monitoring API requests only or setting up alerts for specific events does not provide a holistic view necessary for effective threat detection.

Setting up monitoring with Prometheus

Scenario: An attacker gains access to a backend container and starts many new containers, overwhelming the cluster.What would best prevent this?

Correct answer (Setting resource quotas): Setting resource quotas limits the amount of resources that can be consumed by containers within a namespace, preventing an attacker from overwhelming the cluster by starting excessive containers. Resource quotas help ensure fair resource distribution and protect the cluster from resource exhaustion attacks. Adding more nodes may provide temporary relief but does not address the root cause, and enforcing pod security policies or network segmentation are unrelated to resource consumption.

Setting resource quotas


### Service Mesh

The goals of Istio security?
a. It provides the security by default so no changes are needed to the application code and infrastructure.
b. Integrate with existing security systems to provide multiple layers of defense.
c. Build security solutions on distrusted networks.

- Once CA in istiod successfully validates the credentials carried in the CSR then it signs the CSR to generate the certificate.
- istiod offers a gRPC service to take certificate signing requests (CSRs).
- Istio provides Peer authentication and Request authentication.
- Istio’s authorization features provide mesh-, namespace-, and workload-wide access control for your workloads in the mesh.
- Authorisation supports CUSTOM, ALLOW and DENY actions.
- Peer authentication is used for service-to-service authentication.

All workloads under demo-app namespace must strictly use mutual TLS.

apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: demo-peer-policy
  namespace: demo-app
spec:
  mtls:
    mode: STRICT

Now update the same template to make it a mesh-wide policy, once updated apply the same. --> Change the namespace to istio-system


### Compliance

-  A web application handles user data from Europe and must comply with regulations protecting personal data. Which compliance framework should be prioritized? - GDPR
-  You need to automatically scan your Node.js backend services for vulnerabilities in third-party libraries. Which tools should you use? SYNK OWASP
-  To maintain supply chain compliance in your Kubernetes environment, you want to continuously monitor the performance and security of your cluster. Which tools should you use? PROM & GRAFANA
-  You are required to automate compliance checks and generate audit reports. Which tools are appropriate for this purpose? Chef InSpec and OpenSCAP


### POINTERS
- What are the built-in authentication methods in Kubernetes which are enabled by default? - x509 certificates, Bootstrap tokens, Service account tokens
- Defense in Depth - The 4Cs of Cloud Native Security emphasize applying multiple layers of security controls to protect cloud-native applications, which aligns with the Defense in Depth principle. This principle involves implementing various security measures to safeguard against potential threats.
- What is the security reasoning behind running a Kubernetes cluster on a managed cloud? - Because the applications are only as safe as its underlying infrastructure - A managed cloud provider secures the underlying infrastructure, improving the overall security of the Kubernetes applications.
- Kubelet circumvents kube-scheduler and creates pod locally on the node, making it difficult to track and manage - Static pods are created and managed directly by the Kubelet on each node, bypassing the kube-scheduler. This can make them harder to track and manage centrally since they are not scheduled through the Kubernetes control plane.
- Which of the following is true about Kubernetes namespaces? - They help organize resources within a cluster
- Which control framework focuses on managing and mitigating risks in a cloud environment? - NIST Cybersecurity Framework - The NIST Cybersecurity Framework provides guidelines and best practices for organizations to manage and mitigate cybersecurity risks, including those in cloud environments. It offers a comprehensive approach to identify, protect, detect, respond to, and recover from cyber threats.



- How can you bolster container runtime security within a Kubernetes cluster? - Implementing intrusion detection systems for threat detection
- What is a comprehensive security measure for container runtime environments? - Using SELinux or AppArmor to restrict container privileges (These tools enforce mandatory access controls to limit container actions, reducing the risk of privilege escalation and protecting the host)
- What is an easy way to find regularly appearing Vulnerabilities found in Kubernetes? - Go to the Official CVE Feed in the Kubernetes documentation
- What is the purpose of the kubernetes.io/enforce-mountable-secrets annotation in Kubernetes? - To specify that secrets should be mounted into a pod only if they meet certain criteria defined by the annotation
- How can the OWASP Kubernetes Top Ten guide be utilized to improve security in a Kubernetes environment? - By identifying and mitigating common security risks and vulnerabilities specific to Kubernetes
- Security context requirements for pods - Pod Security Admission ensures that pods adhere to security context rules, such as running as non-root or enforcing SELinux labels.
- Zero Trust Network segmentation using microservices-based architecture - Zero Trust enforces strict access control within microservices, enhancing network security.
-  (The image's SHA256 hash and a public key): Verifying an image's SHA256 hash with a public key ensures its integrity and authenticity, protecting against tampering.
-  
