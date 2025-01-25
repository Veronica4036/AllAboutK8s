## CHEAT SHEET

#### Where is the location specified for the kubelet to look for static pod definition files?

Filesystem-hosted static Pod manifest
Manifests are standard Pod definitions in JSON or YAML format in a specific directory. Use the staticPodPath: <the directory> field in the kubelet configuration file

An alternative and deprecated method is to configure the kubelet on that node to look for static Pod manifests locally, using a command line argument. To use the deprecated approach, start the kubelet with the
--pod-manifest-path=/etc/kubernetes/manifests/ argument.

#### Install a Network Policy Provider
- Use Antrea for NetworkPolicy
- Use Calico for NetworkPolicy
- Use Cilium for NetworkPolicy
- Use Kube-router for NetworkPolicy
- Romana for NetworkPolicy
- Weave Net for NetworkPolicy

#### API Versions

```
k api-resources  | grep v2
horizontalpodautoscalers            hpa          autoscaling/v2                    true         HorizontalPodAutoscaler
```

```
controlplane $ k api-resources | grep -ivE "calico|global"
NAME                                SHORTNAMES   APIVERSION                        NAMESPACED   KIND

----------------------------------------------------- v2 -----------------------------------------------------
horizontalpodautoscalers            hpa          autoscaling/v2                    true         HorizontalPodAutoscaler

----------------------------------------------------- v1 -----------------------------------------------------
certificatesigningrequests          csr          certificates.k8s.io/v1            false        CertificateSigningRequest
leases                                           coordination.k8s.io/v1            true         Lease
endpointslices                                   discovery.k8s.io/v1               true         EndpointSlice
events                              ev           events.k8s.io/v1                  true         Event
runtimeclasses                                   node.k8s.io/v1                    false        RuntimeClass
poddisruptionbudgets                pdb          policy/v1                         true         PodDisruptionBudget
priorityclasses                     pc           scheduling.k8s.io/v1              false        PriorityClass
customresourcedefinitions           crd,crds     apiextensions.k8s.io/v1           false        CustomResourceDefinition
apiservices                                      apiregistration.k8s.io/v1         false        APIService

bindings                                         v1                                true         Binding
componentstatuses                   cs           v1                                false        ComponentStatus
configmaps                          cm           v1                                true         ConfigMap
endpoints                           ep           v1                                true         Endpoints
events                              ev           v1                                true         Event
limitranges                         limits       v1                                true         LimitRange
namespaces                          ns           v1                                false        Namespace
nodes                               no           v1                                false        Node
persistentvolumeclaims              pvc          v1                                true         PersistentVolumeClaim
persistentvolumes                   pv           v1                                false        PersistentVolume
pods                                po           v1                                true         Pod
podtemplates                                     v1                                true         PodTemplate
replicationcontrollers              rc           v1                                true         ReplicationController
resourcequotas                      quota        v1                                true         ResourceQuota
secrets                                          v1                                true         Secret
serviceaccounts                     sa           v1                                true         ServiceAccount
services                            svc          v1                                true         Service

controllerrevisions                              apps/v1                           true         ControllerRevision
daemonsets                          ds           apps/v1                           true         DaemonSet
deployments                         deploy       apps/v1                           true         Deployment
replicasets                         rs           apps/v1                           true         ReplicaSet
statefulsets                        sts          apps/v1                           true         StatefulSet

mutatingwebhookconfigurations                    admissionregistration.k8s.io/v1   false        MutatingWebhookConfiguration
validatingadmissionpolicies                      admissionregistration.k8s.io/v1   false        ValidatingAdmissionPolicy
validatingadmissionpolicybindings                admissionregistration.k8s.io/v1   false        ValidatingAdmissionPolicyBinding
validatingwebhookconfigurations                  admissionregistration.k8s.io/v1   false        ValidatingWebhookConfiguration

selfsubjectreviews                               authentication.k8s.io/v1          false        SelfSubjectReview
tokenreviews                                     authentication.k8s.io/v1          false        TokenReview

localsubjectaccessreviews                        authorization.k8s.io/v1           true         LocalSubjectAccessReview
selfsubjectaccessreviews                         authorization.k8s.io/v1           false        SelfSubjectAccessReview
selfsubjectrulesreviews                          authorization.k8s.io/v1           false        SelfSubjectRulesReview
subjectaccessreviews                             authorization.k8s.io/v1           false        SubjectAccessReview

clusterrolebindings                              rbac.authorization.k8s.io/v1      false        ClusterRoleBinding
clusterroles                                     rbac.authorization.k8s.io/v1      false        ClusterRole
rolebindings                                     rbac.authorization.k8s.io/v1      true         RoleBinding
roles                                            rbac.authorization.k8s.io/v1      true         Role

cronjobs                            cj           batch/v1                          true         CronJob
jobs                                             batch/v1                          true         Job

flowschemas                                      flowcontrol.apiserver.k8s.io/v1   false        FlowSchema
prioritylevelconfigurations                      flowcontrol.apiserver.k8s.io/v1   false        PriorityLevelConfiguration

ingressclasses                                   networking.k8s.io/v1              false        IngressClass
ingresses                           ing          networking.k8s.io/v1              true         Ingress
networkpolicies                     netpol       networking.k8s.io/v1              true         NetworkPolicy

csidrivers                                       storage.k8s.io/v1                 false        CSIDriver
csinodes                                         storage.k8s.io/v1                 false        CSINode
csistoragecapacities                             storage.k8s.io/v1                 true         CSIStorageCapacity
storageclasses                      sc           storage.k8s.io/v1                 false        StorageClass
volumeattachments                                storage.k8s.io/v1                 false        VolumeAttachment

```

### [Network configuration reference](https://www.cni.dev/plugins/current/main/bridge/) 

```

name (string, required): the name of the network.
type (string, required): “bridge”.
bridge (string, optional): name of the bridge to use/create. Defaults to “cni0”.
isGateway (boolean, optional): assign an IP address to the bridge. Defaults to false.
isDefaultGateway (boolean, optional): Sets isGateway to true and makes the assigned IP the default route. Defaults to false.
forceAddress (boolean, optional): Indicates if a new IP address should be set if the previous value has been changed. Defaults to false.
ipMasq (boolean, optional): set up IP Masquerade on the host for traffic originating from this network and destined outside of it. Defaults to false.
ipMasqBackend (string, optional): IP masquerading implementation to use when ipMasq is true. Can be “iptables” or “nftables”. Defaults to “iptables”, unless only “nftables” is available.
mtu (integer, optional): explicitly set MTU to the specified value. Defaults to the value chosen by the kernel.
hairpinMode (boolean, optional): set hairpin mode for interfaces on the bridge. Defaults to false.
ipam (dictionary, required): IPAM configuration to be used for this network. For L2-only network, create empty dictionary.
promiscMode (boolean, optional): set promiscuous mode on the bridge. Defaults to false.
vlan (int, optional): assign VLAN tag. Defaults to none.
preserveDefaultVlan (boolean, optional): indicates whether the default vlan must be preserved on the veth end connected to the bridge. Defaults to true.
vlanTrunk (list, optional): assign VLAN trunk tag. Defaults to none.
enabledad (boolean, optional): enables duplicate address detection for the container side veth. Defaults to false.
macspoofchk (boolean, optional): Enables mac spoof check, limiting the traffic originating from the container to the mac address of the interface. Defaults to false.
disableContainerInterface (boolean, optional): Set the container interface (veth peer inside the container netns) state down. When enabled, IPAM cannot be used.
portIsolation (boolean, optional): Set isolation on the host interface (veth peer in root netns). When enabled containers can communicate only with the host, or through the gateway. Defaults to false.

```

### Storage

```
1. `--mount`: The explicit way to attach storage to containers
```bash
docker run --mount source=my_data,target=/app/data nginx
```

2. `-v` or `--volume`: Quick shorthand to attach storage to containers
```bash
docker run -v my_data:/app/data nginx
```

3. `--volume-driver`: Specifies which storage system to use (like NFS, AWS EBS)
```bash
docker run --volume-driver=nfs -v my_data:/app/data nginx
```
```
