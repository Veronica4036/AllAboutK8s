## CHEAT SHEET

Where is the location specified for the kubelet to look for static pod definition files?

Filesystem-hosted static Pod manifest
Manifests are standard Pod definitions in JSON or YAML format in a specific directory. Use the staticPodPath: <the directory> field in the kubelet configuration file

An alternative and deprecated method is to configure the kubelet on that node to look for static Pod manifests locally, using a command line argument. To use the deprecated approach, start the kubelet with the
--pod-manifest-path=/etc/kubernetes/manifests/ argument.

Install a Network Policy Provider
- Use Antrea for NetworkPolicy
- Use Calico for NetworkPolicy
- Use Cilium for NetworkPolicy
- Use Kube-router for NetworkPolicy
- Romana for NetworkPolicy
- Weave Net for NetworkPolicy
