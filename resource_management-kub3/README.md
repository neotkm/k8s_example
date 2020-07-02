## Практика
### Управление ресурсами
```
kubectl apply -f resource-quota.yaml
kubectl describe  quota -n demo-policies
kubectl delete -f resource-quota.yaml

kubectl apply -f resource_management/limit-range.yaml
kubectl describe limitrange -n demo-policies
kubectl delete -f resource_management/limit-range.yaml
```

