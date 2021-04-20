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
kubectl get po -n svc-without-selector-app -o wide
kubectl delete -f demo-svc-without-selectors.yaml
```  

```
kubectl apply -f demo-svc-with-selectors.yaml
kubectl get svc -n svc-selector-app
kubectl get ep -n svc-selector-app
kubectl get po -n svc-selector-app -o wide
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
kubectl get svc -n external-name-svc-app
kubectl get ep -n external-name-svc-app
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

В этой части занятия используется контейнер "neotkm/pod-healthcheck" с приложением-вебсервисом, которое:
* принимает по http на порту 8080 запросы "/ws/live" и "/ws/ready" и так может сигнализировать о том что контейнер жив и/или готов (отвечая кодом 200) или не готов/не жив
* принимает по http на порту 8080 запросы, переключающие эти его состояния: "/ws/unsetready", "/ws/setready", "/ws/unsetlive", "/ws/setlive"

Таким образом, на первые два запроса мы можем нацелить livenessProbe и readinessProbe, а через остальные - управлять на лету состоянием контейнеров и проверять как k8s реагирует на успех или провал проверок

```
kubectl apply -f demo-ingress.yaml
kubectl get svc -n demo-ingress-app
kubectl get ep -n demo-ingress-app
kubectl get ing -n demo-ingress-app
```
Используем для управления контейнерами в поде контейнер netshoot (https://github.com/nicolaka/netshoot). Запускаем в нём консоль и выясняем внешний адрес кластера, чтобы отправлять на него запросы.
```
kubectl run --generator=run-pod/v1 tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
или
kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
export ip=`curl -s ifconfig.io`
echo $ip  
```
Курлим адрес /ws/unsetready и переключаем один из контейнеров в режим не готовности (приложение в нём перестанет одавать код 200 по запросу /ws/ready, прописанному в livenessProbe)
```
curl -v http://$ip/ws/unsetready -H 'Host: example.com'
```
Видим что k8s убирает адрес одного контейнера из энпоинтов:
```
kubectl get ep -n demo-ingress-app
```
Видим что один из контейнеров перешел в not ready (обращаем внимание на его адрес)
```
kubectl get po -o wide -n demo-ingress-app
```
При этом он продолжает проходить проверку livenessProbe - k8s успешно открывает url "/ws/live" напрямую по адресу ноды, и поэтому не перезапускает контейнер. Попробуем вернуть контейнер в состояние ready. Он убран из эндпоинтов и теперь к нему по http://$ip/ не достучаться. Но можно достучаться напрямую к ноде по порту 8080:
```
curl -v http://<адрес ноды>:8080/ws/live -H 'Host: example.com'
```
Курлим запрос, возвращающий приложение в режим готовности.
```
curl -v http://<адрес ноды>:8080/ws/setready -H 'Host: example.com'
```
Видим что контейнер перешел в ready и k8s добавил его адрес в энпоинты:
```
kubectl get ep -n demo-ingress-app
kubectl get po -o wide -n demo-ingress-app
```

Убиваем приложение
```
curl -v http://$ip/ws/unsetlive -H 'Host: example.com'
```
Смотрим на поды, видим что проверка сработала и контейнер перезапускается
```
kubectl get po -o wide -n demo-ingress-app -w
```

Удаляем всё
```
kubectl delete -f demo-ingress.yaml  
```

Меняем настройки livenessProbe на TCP Socket в demo-ingress.yaml 
```
         livenessProbe:
            failureThreshold: 2
            tcpSocket:
              port: 8080
            initialDelaySeconds: 25
            periodSeconds: 5
```            
Убеждаемся что всё работает стабильно, приложение действительно слушает запросы http на порту 8080

```            
kubectl apply -f demo-ingress.yaml
kubectl get po -o wide -n demo-ingress-app -w
kubectl delete -f demo-ingress.yaml

```            
Меняем настройки livenessProbe на заведомо неработающий порт 8088
```   
         livenessProbe:
            failureThreshold: 2
            tcpSocket:
              port: 8088
            initialDelaySeconds: 25
            periodSeconds: 5
```   
Запускаем и убеждаемся что контейнеры постоянно рестартуют		
```   
kubectl apply -f demo-ingress.yaml
kubectl get po -o wide -n demo-ingress-app -w
```   
Останавливаем под
```   
kubectl delete -f demo-ingress.yaml
```   

