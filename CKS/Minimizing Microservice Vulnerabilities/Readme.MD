## Minimizing microservice vulnerabilities
The Minimizing Microservice Vulnerabilities domain retains its weight at 20%, but several key updates have been introduced to align with modern security practices.

- Pod Security Standards: A shift in focus from OS-level security domains to using pod security standards is a significant change. Pod security standards define policies that restrict what pods can do, ensuring they operate with the least privilege required.
- Managing Kubernetes Secrets: Managing Kubernetes secrets securely remains essential. This involves securely storing and accessing sensitive information like API keys, tokens, and passwords.
- Isolation Techniques: The recommendation to use container runtime sandboxes has been expanded to a broader focus on implementing isolation techniques. This includes using multi-tenancy and sandboxed containers, among other methods.
- Pod-to-Pod Encryption with Cilium: Another important update is the specific mention of using Cilium for pod-to-pod encryption. Cilium enhances Kubernetes security by providing advanced networking and security policies, including robust encryption capabilities.
