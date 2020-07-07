## Практика
### 1. Сервисы: Without Selector / Selector  
### 2. Сервисы: Multiport  
### 3. Сервисы: СlusterIP  
### 4. Сервисы: NodePort 
### 5. Сервисы: LoadBalancer
### 6. Сервисы: ExternalName    
### 7. Сервисы: Headless  
### 8. Сервисы: External IPs  
### 9. Сервисы: Ingress + Health Check



--
### Сервисы: Without Selector / Selector  

```
kubectl apply -f demo-svc-without-selectors.yaml
kubectl get svc -n svc-without-selector-app  
kubectl get ep -n svc-without-selector-app  
kubectl delete -f demo-svc-without-selectors.yaml
```  

```
kubectl apply -f demo-svc-with-selectors.yaml
kubectl get svc -n svc-selector-app
kubectl get ep -n svc-selector-app
kubectl delete -f demo-svc-with-selectors.yaml
```

--
### Сервисы: Multiport  

```
kubectl apply -f demo-svc-multiport.yaml
kubectl get svc -n demo-svc-multiport-app
kubectl get ep -n demo-svc-multiport-app
kubectl delete -f demo-svc-multiport.yaml
```

--
### Сервисы: СlusterIP  

```
kubectl apply -f demo-cluster-ip-svc.yaml
kubectl get svc -n cluster-ip-svc-app cluster-ip-svc-app
kubectl get ep -n cluster-ip-svc-app cluster-ip-svc-app
kubectl delete -f demo-cluster-ip-svc.yaml
```

--
### Сервисы: NodePort  
```
kubectl apply -f demo-node-port-svc.yaml
kubectl get svc -n node-port-svc-app
kubectl get ep -n node-port-svc-app
kubectl delete -f demo-node-port-svc.yaml
```

--
### Сервисы: LoadBalancer  
```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
  clusterIP: 10.96.0.111
  type: LoadBalancer
status:
  loadBalancer:
    ingress:
    - ip: 192.0.2.127
```

--
### Сервисы: ExternalName  
```
kubectl apply -f demo-external-name-svc.yaml
kubectl get svc -n extarnal-name-svc-app
kubectl get ep -n extarnal-name-svc-app
kubectl delete -f demo-external-name-svc.yaml
```

--
### Сервисы: Headless  
```
kubectl apply -f demo-svc-headless-with-selectors.yaml
kubectl get svc -n svc-headless-selector-app
kubectl get ep -n svc-headless-selector-app
kubectl delete -f demo-svc-headless-with-selectors.yaml
```

--
### Сервисы: External IPs  
```
kubectl apply -f demo-svc-external-ips.yaml
kubectl get svc -n demo-svc-external-ips-app
kubectl get ep -n demo-svc-external-ips-app
kubectl delete -f demo-svc-external-ips.yaml
```

--
### Сервисы: Ingress + Health Check

```
kubectl apply -f demo-ingress.yaml
kubectl get svc -n demo-ingress-app
kubectl get ep -n demo-ingress-app
kubectl get ing -n demo-ingress-app

kubectl run --generator=run-pod/v1 tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
export ip=`curl -s ifconfig.io`
echo $ip  

curl -v http://$ip/ws/unsetready -H 'Host: example.com'
curl -v http://$ip/ws/unsetlive -H 'Host: example.com'


kubectl delete -f demo-ingress.yaml  

```

