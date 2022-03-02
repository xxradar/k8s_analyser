```
#!/bin/bash
namespace="app-routable-demo"

#for netpol in $(kubectl get netpol -n $namespace -o=json |  jq -r '.items[].metadata.name') ; do
for netpol in $(kubectl get netpol -n $namespace  -o json | jq -r '.items[] | select (.spec.podSelector.matchLabels != null ) |  [.metadata.name] |  @tsv') ; do

  echo "####### Processing namespace "${namespace}
  echo "####### Checking for network policy "$netpol
  podselector=$(kubectl get netpol $netpol -n $namespace -o json | jq -r '.spec.podSelector.matchLabels | keys[] as $k |  "\($k)=\(.[$k])"' 2>/dev/null)
  echo "####### The pod selector is: $podselector"

  if [ -z "$podselector" ]
    then echo "NULL selector"
    else kubectl get po -l $podselector -n $namespace -o json | jq -r '.items[].metadata.name'
    fi
done
```
