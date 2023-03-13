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
