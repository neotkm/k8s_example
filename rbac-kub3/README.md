## Практика
### RBAC
```
kubectl apply -f demo-namespace.yaml
kubectl apply -f pod-read-create-role.yaml
kubectl apply -f pod-read-create-rolebinding.yaml
kubectl auth can-i get po --namespace rbac-demo --as developer
kubectl auth can-i get po --namespace kube-system --as developer
kubectl auth can-i delete po --namespace rbac-demo --as developer
```
