To obtain the json file, run
```
kubectl get pods --all-namespaces -o json >test.json
```

To list all the pod names
```
cat test.json | jq '.items[].metadata.name'
```

To select a specific pod name and print out name and uid
```
cat test.json | jq '.items[].metadata | select (.name == "test-6664f97c7f-8n2h9") | [.name, .uid]'
```

