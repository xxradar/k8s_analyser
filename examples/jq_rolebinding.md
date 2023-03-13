### Analyzing ClusterRoleBindings
Find the rolebinding for a specific service account
```
kubectl get clusterrolebindings -o json | jq -r '
  .items[] |
  select(
    .subjects // [] | .[] |
    [.kind,.namespace,.name] == ["ServiceAccount","kube-system","kube-proxy"]
  ) |
  .metadata.name'
```
Get a list of all bindings, roles and subjects
```
kubectl get clusterrolebindings -o json | jq -r '
  .items[] |
  select(
    .subjects // [] | .[] 
  ) |  [ .metadata.name, .roleRef.name, .subjects[].kind, .subjects[].name]'
```  
```
kubectl get rolebindings -A -o json | jq -r '
  .items[] |
  select(
    .subjects // [] | .[] 
  ) |  [ .metadata.name, .roleRef.name, .subjects[].kind, .subjects[].name]'  
```


### Troubleshooting SA
```
kubectl get pods  -o json | jq -r '.items[] | {pod_name: .metadata.name, service_account: .spec.serviceAccountName}'
```
```
kubectl get pods -n test -o json | jq -rc '.items[] | {pod_name: .metadata.name, service_account: .spec.serviceAccountName, uses_hostPID: (.spec.hostPID // false)}'
```

### Script (WIP)
```
#!/bin/bash
output=$(kubectl get pods -n $1 -o json | jq -r '.items[] | {pod_name: .metadata.name, service_account: .spec.serviceAccountName, uses_hostPID: (.spec.hostPID // false)}')

## Overview of Pod_name , service_account and hostPID
echo $output | jq -r


for SALIST in $(echo $output | jq -r .service_account)
do
        rolebinding=$(kubectl get clusterrolebindings -o json | jq -r --arg sa_list $SALIST  --arg namespace $1 '.items[] | select(.subjects // [] | .[] | [.kind,.namespace,.name] == ["ServiceAccount", $namespace, $sa_list]) | .metadata.name')
        echo $rolebinding
        role=$(kubectl get clusterrolebinding  $rolebinding -o json -A | jq -r '.roleRef.name')
        echo $role
        kubectl describe clusterrole $role
done
```
