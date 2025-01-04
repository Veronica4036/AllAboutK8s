# CKS CheatSheet


[Image: image.png]

### Set few shortcuts:

```
vim ~/.vimrc

set expandtab
set tabstop=2
set shiftwidth=2

source ~/.vimrc

===================

vim ~/.bashrc

alias k=kubectl
alias kgn="kubectl get nodes"
alias kgp="kubectl get pods"
alias kgks="kubectl get pods -n kube-system"
alias kdp="kubectl delete pod"
alias kg="kubectl get"
alias kdel="kubectl delete"
alias kd="kubectl describe"
alias ke="kubectl edit"
alias kc="kubectl create"
alias ka="kubectl apply -f"
alias kdy="kubectl -o yaml --dry-run=client"

source ~/.bashrc
```




## Docker basic

1. docker ps -a (list all containers)
2. docker exec <container name> <command>
3. docker rm <container name> —force
4. `docker run --name=<name> <image name> <command>`
5. docker run --name app2 --pid=container:app1 -d nginx:alpine sleep infinity #(Run second container in first containers PID namespace)
6. docker inspect <image> (to get some details like pid)





## Some Basic but not so frequently used commands:

Unzip Tar file

```
tar -xvf file.tar
```


Send file from CP to node

```
scp /root/profile node01:/root
```


tr means replace

```
k config view -o jsonpath="{.contexts[*].name}" | tr " " "\n"
```


How to check ownership of a directory. e.g: ownership of directory `/var/lib/etcd`: 

```
ls -lh /var/lib | grep etcd

 -l   use a long listing format
 -h, --human-readable       with -l and -s, print sizes like 1K 234M 2G etc.
```


view file permissions:


```
stat -c %a /var/lib/etcd
700

The command `stat ``-``c ``%``a ``/``var``/``lib``/``kubelet``/``config``.``yaml` is used to display the 
permissions of the file `/``var``/``lib``/``kubelet``/``config``.``yaml` in numeric (octal) format. 
```


binary checks:

```
sha512sum <binary>

If they are unique, you will ll just get one entry below:
cat compare | uniq
```



### Important locations:

kubelet config location:

```
/var/lib/kubelet/config.yaml
```


what I never knew about grep:

```
`grep containerLog /var/lib/kubelet/config.yaml`
```





### Cilium Netpol:

```
# cks7262:~/8_p1.yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: p1
  namespace: team-orange
spec:
  endpointSelector:
    matchLabels:
      type: messenger
  egressDeny:
  - toEndpoints:
    - matchLabels:
        type: database  # we use the label of the Pods behind the Service "database"
        
        
        
# cks7262:~/8_p2.yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: p2
  namespace: team-orange
spec:
  endpointSelector:
    matchLabels:
      type: transmitter # we use the label of the Pods behind Deployment "transmitter"
  egressDeny:
  - toEndpoints:
    - matchLabels:
        type: database  # we use the label of the Pods behind the Service "database"
    icmps:
    - fields:
      - type: 8
        family: IPv4
      - type: EchoRequest
        family: IPv6
        
        
        
# cks7262:~/8_p3.yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: p3
  namespace: team-orange
spec:
  endpointSelector:
    matchLabels:
      type: database
  egress:
  - toEndpoints:
    - matchLabels:
        type: messenger
    authentication:
      mode: "required"  
```



### Basics of openSSL;

**openssl genrsa -out <key_name.key> 2048**
**openssl req -new -key <key_generated_above> -out <csr_name.csr>**

**Creating a Private Key**:

    * `openssl genrsa -out 60099.key 2048`
    * This command generates a new RSA private key with a length of 2048 bits and saves it in the file `60099.key`.
    * The `genrsa` command is used to generate an RSA private key.
    * The `-out` option specifies the output file name for the private key.

**Creating a Certificate Signing Request (CSR)**:

    * `openssl req -new -key 60099.key -out 60099.csr`
    * the `req` stands for "request"
    * This command creates a new Certificate Signing Request (CSR) based on the provided private key `60099.key`.
    * The CSR contains information about the entity (e.g., user, server) for which the certificate is being requested.
    * The `-new` option specifies that a new CSR should be generated.
    * The `-key` option specifies the private key file to be used for the CSR.
    * The `-out` option specifies the output file name for the CSR.
    * During the execution of this command, you will be prompted to enter various details, such as the Common Name (CN), Organization (O), and other information.


Now to create a csr, we need to decode the csr content:

```
cat 60099.csr | base64 -w 0
```























CKS SPECIFIC ========================================



### CIS Benchmarks fix Controlplane:

How to check for any test case?

```
controlplane $ kube-bench | grep 1.2.20
[FAIL] 1.2.20 Ensure that the --profiling argument is set to false (Automated)
1.2.20 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
```

Then follow the suggestions to make the fixes!




### Verify Platform Binaries:

Download the binary from the question and use the command sha512sum to get the sha value

```
sha512sum kubernetes/server/bin/kubelet
```


For the 2nd sha:

* for kubelet - `/usr/bin/kubelet`
* for any cp component, follow the below steps:

```
crictl ps | grep api
ea69d463f1f44       604f5db92eaa8       6 minutes ago       Running             kube-apiserver            1                   859e337e9f6fa       kube-apiserver-controlplane

copy the container name "kube-apiserver"

 ps aux | grep kube-apiserver
root        2400  3.0 12.1 1518620 246572 ?      Ssl  07:32   0:13 kube-apiserver --advertise-address=172.30.1.2 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
root        7968  0.0  0.0   3436   720 pts/0    S+   07:39   0:00 grep --color=auto kube-apiserver

use the PID which is not for --color=auto

controlplane $ find /proc/2400/root/ | grep kube-apiserver
/proc/<pid>/root/usr/local/bin/kube-apiserver
/proc/2400/root/usr/local/bin/kube-apiserver

controlplane $ sha512sum /proc/2400/root/usr/local/bin/kube-apiserver
540e1cb75c64c0fd1d8bf06f27bdba449a2145421986f029d2d64df22defb0f25bb55e17bee20d3431afe1d410b3a7795b0c396de7c0947d53001da78e620051  /proc/2400/root/usr/local/bin/kube-apiserver
```



### RBAC:

```
Role → RoleBinding ✅
Role → ClusterRoleBinding ❌
ClusterRole → RoleBinding ✅
ClusterRole → ClusterRoleBinding ✅
```



APPARMOR:

Check existing profiles

```
ssh node01
    apparmor_status
```


to add profile:

```
apparmor_parser <path to profile>
```


