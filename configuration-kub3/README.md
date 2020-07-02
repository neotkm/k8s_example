## Практика
### 1. ConfigMap
### 2. Secret
### 3. IngressNginx + basic-auth + Secret
### 4. Secret + docker-registry  

--
### ConfigMap 
Создаем ConfigMap и Pod которые их используют:  
`kubectl apply -f demo-config-map-pod.yaml`    

Смотрим что у нас получилось с Pod c переменными окружения из манифеста:  
`kubectl logs -f -n  demo-config-map-pod demo-config-map-pod-env-manifest`

Смотрим что у нас получилось с Pod c переменными окружения из ConfigMap:  
`kubectl logs -f -n  demo-config-map-pod app-config-env-pod`  

Смотрим что у нас получилось с Pod c переменными окружения из ConfigMap:  
`kubectl logs -f -n  demo-config-map-pod app-config-env-pod-2`  

Смотрим что у нас получилось с Pod c переменными окружения из ConfigMap:  
`kubectl logs -f -n  demo-config-map-pod app-config-env-pod-3`  

Смотрим что у нас получилось с Pod c переменными окружения из ConfigMap и проброса их в CMD:
`kubectl logs -f -n  demo-config-map-pod app-config-env-pod-4`  

Что получается при использовании ConfigMap в виде файлов:
`kubectl logs -f -n  demo-config-map-pod app-config-env-pod-5`  

Что получается при использовании ConfigMap в виде файлов:
`kubectl logs -f -n  demo-config-map-pod app-config-env-pod-6`  

Удаляем наши ConfigMap и Pod:  
`kubectl delete -f demo-config-map-pod.yaml`  

--
### Secret

Создаем Secret и Pod которые их используют:  
`kubectl apply -f demo-secret-pod.yaml`    

Смотрим что у нас получилось с Pod c Secret в ENV:
`kubectl logs -f -n demo-secret-pod demo-secret-pod` 

Смотрим что у нас получилось с Pod c Secret в ENV, один ключ:
`kubectl logs -f -n demo-secret-pod demo-secret-pod-2`

Смотрим что у нас получилось с Pod c переменными окружения из Secret и проброса их в CMD:  
`kubectl logs -f -n demo-secret-pod demo-secret-pod-3`  

Содержимое Secret в виде файлов:  
`kubectl logs -f -n demo-secret-pod demo-secret-pod-4`  

Содержимое Secret в виде файлов:  
`kubectl logs -f -n demo-secret-pod demo-secret-pod-5`  

Удаляем ранее созданное:
`kubectl delete -f configuration/demo-secret-pod.yaml`


--
### IngressNginx + basic-auth + Secret

```
htpasswd -c auth foo
kubectl create secret generic basic-auth --from-file=auth

kubectl apply -f demo-ingress-basic-auth.yaml

export ip=`curl -s ipconfig.io`
echo $ip
curl -v http://$ip/ -H 'Host: foo.bar.com'
curl -v http://$ip/ -H 'Host: foo.bar.com' -u 'foo:baaar'

kubectl delete -f configuration/demo-ingress-basic-auth.yaml
```

--
### Secret + docker-registry

```
kubectl create secret -n demo-pull-secret \
docker-registry demo-pull-secret \
--docker-server=https://index.docker.io/v1/ \
--docker-username=DOCKER_USER \
--docker-password=DOCKER_PASSWORD \
--docker-email=DOCKER_EMAIL
```

```
kubectl apply -f demo-pull-secret.yaml
```

