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

