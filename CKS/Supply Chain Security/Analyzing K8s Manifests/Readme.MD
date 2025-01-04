Run the following command to analyze the manifest with KubeLinter:
kube-linter lint nginx.yml
This command will output the analysis results, indicating any issues or best practices not being followed in the deployment manifest.

Make a checklist of things we should be checking for example:
```
kube-linter lint nginx.yml 
KubeLinter 0.7.1

/root/nginx.yml: (object: <no namespace>/nginx-deployment apps/v1, Kind=Deployment) object has 3 replicas but does not specify inter pod anti-affinity (check: no-anti-affinity, remediation: Specify anti-affinity in your pod specification to ensure that the orchestrator attempts to schedule replicas on different nodes. Using podAntiAffinity, specify a labelSelector that matches pods for the deployment, and set the topologyKey to kubernetes.io/hostname. Refer to https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity for details.)

/root/nginx.yml: (object: <no namespace>/nginx-deployment apps/v1, Kind=Deployment) container "nginx" does not have a read-only root file system (check: no-read-only-root-fs, remediation: Set readOnlyRootFilesystem to true in the container securityContext.)

/root/nginx.yml: (object: <no namespace>/nginx-deployment apps/v1, Kind=Deployment) container "nginx" is not set to runAsNonRoot (check: run-as-non-root, remediation: Set runAsUser to a non-zero number and runAsNonRoot to true in your pod or container securityContext. Refer to https://kubernetes.io/docs/tasks/configure-pod-container/security-context/ for details.)

/root/nginx.yml: (object: <no namespace>/nginx-deployment apps/v1, Kind=Deployment) container "nginx" has cpu request 0 (check: unset-cpu-requirements, remediation: Set CPU requests for your container based on its requirements. Refer to https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits for details.)

/root/nginx.yml: (object: <no namespace>/nginx-deployment apps/v1, Kind=Deployment) container "nginx" has memory limit 0 (check: unset-memory-requirements, remediation: Set memory limits for your container based on its requirements. Refer to https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits for details.)

Error: found 5 lint errors

```

1 example:
Without a read-only root filesystem, the container's root filesystem is writable, which increases security risks. An attacker or process could modify files in the root filesystem, leading to potential vulnerabilities. Setting the root filesystem to read-only prevents these unauthorized modifications.

