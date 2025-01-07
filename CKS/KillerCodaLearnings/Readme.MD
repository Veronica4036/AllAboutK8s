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
   Location: Container logs (crictl logs) `/var/log/containers/kube-apiserver-*.log` or Pod logs `/var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*.log`

2. **Misconfigured etcd Connection**
   ```yaml
   spec:
     containers:
     - command:
       - kube-apiserver
       - --etcd-servers=this-is-very-wrong
   ```
   Error: `Error while dialing dial tcp: address this-is-very-wrong: missing port in address`
   Location: Container logs (crictl logs) `/var/log/containers/kube-apiserver-*.log` or Pod logs `/var/log/pods/kube-system_kube-apiserver-*/kube-apiserver/*.log`

   ```yaml
   spec:
     containers:
     - command:
       - kube-apiserver
       - --etcd-servers=https://127.0.0.1:23000  # Incorrect port
   ```
   Error in logs: 
   ```
   grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:23000", ServerName: "127.0.0.1:23000", }. Err: connection error: desc = "transport: Error 
   while dialing: dial tcp 127.0.0.1:23000: connect: connection refused"
   ```
   Fix: Change etcd server port to 2379 in kube-apiserver.yaml
   
   *Note* - GRPC connection errors often indicate etcd communication issues.

3. **Invalid YAML in Manifest**

   If you don't find container or pod logs, it often indicates a YAML issue.

   Error: `Could not process manifest file err="...couldn't parse as pod..."`
   Location: Kubelet logs (/var/log/syslog or journalctl)

   To troubleshoot:
   a. Check syslog/journalctl:
      ```bash
      cat /var/log/syslog | grep -i "manifest" | head -n 2
      (OR)
      journalctl | grep apiserver | grep manifest | head -n 2
      ```
      Example output:
      ```
      Jan  7 04:54:46 controlplane kubelet[1730]: I0107 04:54:46.735443    1730 kubelet.go:303] "Adding static pod path" path="/etc/kubernetes/manifests"
      Jan  7 04:57:15 controlplane kubelet[1730]: E0107 04:57:15.745994    1730 file.go:108] "Unable to process watch event" err="can't process config file \"/etc/kubernetes/manifests/kube-apiserver.yaml\": /etc/kubernetes/manifests/kube-apiserver.yaml: couldn't parse as pod(yaml: line 4: could not find expected ':'), please check config file"
      ```

   b. Inspect the manifest:
      ```bash
      cat /etc/kubernetes/manifests/kube-apiserver.yaml | head -n 5
      apiVersion: v1
      kind: Pod
      metadata;
        annotations:
          kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 172.30.1.2:6443
      ```

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
