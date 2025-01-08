## Cluster Setup Domain Updates (Weightage Increased from 10% to 15%)
The Cluster Setup domain sees a notable increase in its weight from 10% to 15%, indicating its growing importance in securing Kubernetes clusters.

- Network Security Policies: The focus on configuring network security policies to restrict cluster-level access continues. This practice remains essential for isolating and protecting the Kubernetes cluster from unauthorized access.
- CIS Benchmarks: Using CIS (Center for Internet Security) benchmarks to review the security configurations of key Kubernetes components (such as etcd, kubelet, kubeapi, and kubedns) is still a critical component of this domain. Adhering to these benchmarks ensures the cluster is secured according to industry standards.
- Ingress Setup with TLS: One of the major updates is the emphasis on setting up ingress with Transport Layer Security (TLS). TLS is essential for encrypting data in transit and ensuring secure communication between services and clients.
- Node Metadata Protection: Protecting node metadata and endpoints remains a priority, with the continued recommendation to verify platform binaries before deployment. However, the previous guideline to minimize GUI access has been removed, shifting the focus towards more specific and actionable security measures.
