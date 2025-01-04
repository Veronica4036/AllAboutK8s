## Kubernetes system hardening
- Minimizing OS Footprint: Reducing the host operating system (OS) footprint to essential components remains a best practice. This helps in eliminating unnecessary software that could be exploited by attackers.
- Least-Privilege IAM: The previous guidance on minimizing IAM roles has been refined to emphasize the principle of least-privilege identity and access management (IAM). This ensures that users and services only have the permissions necessary to perform their tasks.
- Restricting Network Access: Minimizing external access to the cluster’s network reduces the risk of external attacks.
- Kernel Hardening Tools: The use of tools like AppArmor and seccomp continues to be a crucial practice for enforcing security policies that limit the capabilities of processes running on the system.

The System Hardening domain focuses on reducing the attack surface at the system level, with a slight reduction in its weight from 15% to 10%. However, the key practices remain consistent with the previous curriculum.
