---
title: "Kubernetes Cheatsheet"
tags: ["cheatsheet", "k8s", "kubernetes"]
toc: true
---

For instance, we'll consider placeholder name as revoir-service, format could be `json|yaml|wide|name`
## Installation
create a new deployment/service from yaml definition
```shell
kubectl create -f revoir-service.yaml
```
post creation you can watch pods coming up
```shell
kubectl get pods -w
```
To get deployment details as yaml
```shell
kubectl get deploy/revoir-service -o yaml
```

## Debugging
To view logs, port-forward you don't need specific pod, you can do the same with deployment, it'll use a random pod
- `--timestamps` useful as you want to associate logs with time
- `--tail=lastN` or `--since=10s/10m/10h`
```shell
kubectl logs -f deploy/revoir-service --timestamps --tail=1
```

You can filter resources like pod/deployment/service/... with label
```shell
kubectl get pods -l 'release=revoir-service'
```
Forward a port from k8s pod 8080 to localhost 8090
```shell
kubectl port-forward deploy/revoir-service 8090:8080
```

## Admin Commands

get top `[nodes|pods]` resource consumption
- `cpu|memory` can be passed for `sort-by`
```shell
kubectl top nodes --sort-by='cpu'
```
if you're on k8s version older than 1.18, to deal with sort [issue](https://github.com/kubernetes/kubernetes/pull/82333) pipe to sort
```shell
kubectl top nodes --sort-by='cpu' | sort --reverse --key 3 --numeric
```
once you've found top nodes, to get pods specific to a node
```shell
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=gke-cluster-node-name
```
scale replicas for a deployment
```shell
kubectl scale deployment/revoir-service --replicas=4
```
if max replicas limit is reached then this command wouldn't have any effects beyond the limit

rolling restart of all pods in deployment
```shell
kubectl rollout restart deploy/revoir-service
```

> Official k8s [cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
