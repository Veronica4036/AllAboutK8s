# Learnings from [KillerCoda CKS playlist](https://killercoda.com/killer-shell-cks)

Here, we'll cover some good to know things, which are not part of any module

## Kubernetes API Server Troubleshooting Guide

### Common Failure Scenarios

1. **Incorrect Command-Line Arguments**
   ```yaml
   spec:
     containers:
     - command:
       - kube-apiserver
       - --this-is-very-wrong
   ```
   Error: `Error: unknown flag: --this-is-very-wrong`
   Location: `/var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*.log`

2. **Misconfigured etcd Connection**
   ```yaml
   spec:
     containers:
     - command:
       - kube-apiserver
       - --etcd-servers=this-is-very-wrong
   ```
   Error: `Error while dialing dial tcp: address this-is-very-wrong: missing port in address`
   Location: Container logs (crictl logs)

3. **Invalid YAML in Manifest**
   ```yaml
   apiVersionTHIS IS VERY ::::: WRONG v1
   ```
   Error: `Could not process manifest file err="...couldn't parse as pod..."`
   Location: Kubelet logs (/var/log/syslog or journalctl)

### Key Log Locations

- Pod logs: `/var/log/pods/kube-system_kube-apiserver-*/*.log`
- Container logs: `crictl logs <container_id>`
- Kubelet logs: `/var/log/syslog` or `journalctl -u kubelet`

### Quick Troubleshooting Steps

1. Backup: 
   ```bash
   sudo mkdir -p /etc/kubernetes/backup && \
   sudo cp /etc/kubernetes/manifests/kube-apiserver.yaml /etc/kubernetes/backup/kube-apiserver.yaml.$(date +%Y%m%d%H%M%S)
   ```
2. Check status: `kubectl -n kube-system get pod | grep apiserver`
3. Monitor containers: `watch crictl ps`
4. Check logs: `crictl logs $(crictl ps -q --name kube-apiserver)`
5. Kubelet logs: `journalctl -u kubelet | grep apiserver`
6. Restore if needed: 
   ```bash
   sudo cp /etc/kubernetes/backup/kube-apiserver.yaml.* /etc/kubernetes/manifests/kube-apiserver.yaml
   ```

### Best Practices

- Always backup before changes
- Start with container logs, then system logs
- Allow time for API server restart
- Force restart if needed:
  ```bash
  sudo mv /etc/kubernetes/manifests/kube-apiserver.yaml /tmp/
  # Wait for container removal
  sudo mv /tmp/kube-apiserver.yaml /etc/kubernetes/manifests/
  ```
