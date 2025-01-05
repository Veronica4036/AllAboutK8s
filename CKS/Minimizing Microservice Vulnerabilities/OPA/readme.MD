what is OPA

opa default port

Opa format:
```
cat example.rego 
package httpapi.authz
import input
default allow = 
allow {
 input.path == "home"
 input.user == "Kedar"
 } 
controlplane ~ ✦ ➜  vi exa
```

how to load OPA files:
```
Run Below command to import sample.rego in OPA

curl -X PUT --data-binary @sample.rego http://localhost:8181/v1/policies/samplepolicy
```

explain the command nicely

Create a configmap for OPA using the untrusted-registry.rego policy


Use below files:

configmap file: /root/untrusted-registry.rego

configmap name : untrusted-registry

controlplane ~ ➜  kubectl create configmap untrusted-registry --from-file=untrusted-registry.rego
configmap/untrusted-registry created




We have placed rego policies under /root/untrusted-registry.rego and /root/unique-host.rego. View the contents of these files and identify which kubernetes resources will be validated by these rego policies?
Check input.request.kind.kind in rego policies file



```
Create a pod defined under /root/test.yaml in the namespace dev. Fix the OPA validation issue while creating the pod.


NOTE: The pod is expected to be in a created state but not up and running.

Run

kubectl apply -n dev -f /root/test.yaml
You will get an error from OPA as below

Error from server: error when creating "/root/test.yaml": admission webhook "validating-webhook.openpolicyagent.org" denied the request: image 'nginx' comes from untrusted registry

Correct below line in /root/test.yaml

image: hooli.com/nginx
then run

kubectl apply -n dev -f /root/test.yaml
NOTE: The pod is expected to be in a created state but not up and running.
```



