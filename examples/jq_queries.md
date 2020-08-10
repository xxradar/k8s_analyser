### To obtain the json file, run ...
```
kubectl create --rm -it --image xxradar/hackon test1 -- bash
kubectl create --rm -it --image xxradar/hackon test2 -- bash
kubectl get pods --all-namespaces -o json >test.json
```
Pleate note that test.json (so you need to rerun again if you're starting / stopping new pods<br>


### To list all the pod names
```
cat test.json | jq '.items[].metadata.name'
```

### To select a specific pod name and print out name and uid
```
cat test.json | jq '.items[] | select (.metadata.name == "test-6664f97c7f-8n2h9") | .metadata.name'
cat test.json | jq '.items[] | select (.metadata.name == "test-6664f97c7f-8n2h9") | .metadata.name, .metadata.uid'
```
```
cat test.json | jq '.items[] | select (.metadata.name == "test-6664f97c7f-8n2h9") |  [.metadata.name, .metadata.uid]'
cat test.json | jq '.items[] | select (.metadata.name == "test-6664f97c7f-8n2h9") | [.metadata.name, .metadata.uid, .status.containerStatuses[].containerID]'
```
### To find based on a keyword 
```
cat test.json | jq '.items[] | select(.metadata.name | startswith("test")) |  [.metadata.name, .metadata.uid]'
```


### Interesting examples found on the internet ...
```
curl example.com/json | jq '.[].properties | select(.type | startswith("dev"))'
```
