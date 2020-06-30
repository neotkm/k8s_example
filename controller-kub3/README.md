## Практика
### 1. Namespace
### 2. Pod
### 3. Replicaset
### 4. Deployment
### 5. Daemonset
### 6. Statefulset 
 
--
### Namespace  
Проверяем какие Namespace у нас есть:  
`kubectl get ns`

Создаем Namespace:  
`kubectl apply -f example-namespace.yaml`

Проверяем изменения Namespace:  
`kubectl get ns`

Смотрим более подробную информацию о Namespace:  
`kubectl describe ns example`

Удаляем Namespace:  
`kubectl delete ns example`

--
### Pod

Создаем Pod:  
`kubectl apply -f pod-demo.yaml`

Попадаем в Pod:  
`kubectl exec -it -n pod-demo pod-demo sh`  

Удаляем Pod:  
`kubectl delete -f pod-demo.yaml`

Создаем Pod c Init контейнером:  
`kubectl apply -f pod-demo-init.yaml`

Смотрим описание Pod:  
`kubectl describe po -n pod-demo pod-demo`

Удаляем Pod c Init контейнером:  
`kubectl delete -f pod-demo-init.yaml`

--
### Replicaset

Создаем Replicaset:  
`kubectl apply -f replicaset-demo.yaml`

Смотрим количество Pod, запущенных Replicaset:  
`kubectl get po -n  replicaset-demo`

Изменяем количество Pod, запущенных Replicaset:  
`kubectl scale --replicas=5 rs -n replicaset-demo replicaset-demo`

Смотрим количество Pod, запущенных Replicaset еще раз:  
`kubectl get po -n  replicaset-demo`

Смотрим описание Replicaset:  
`kubectl describe rs -n replicaset-demo`

Удаляем Replicaset:  
`kubectl delete -f replicaset-demo.yaml`

--
### Deployment

Создаем Deployment:  
`kubectl apply -f deployment-demo.yaml`

Смотрим количество Pod, запущенных Deployment:  
`kubectl get po -n  deployment-demo`

Изменяем количество Pod, запущенных Deployment:  
`kubectl scale --replicas=5 deployment -n deployment-demo deployment-demo`

Смотрим количество Pod, запущенных Deployment еще раз:  
`kubectl get po -n  deployment-demo`

Смотрим описание Deployment:  
`kubectl describe deployment -n deployment-demo`

Смотрим описание Replicaset в Deployment:  
`kubectl describe rs -n deployment-demo`

Удаляем Deployment:  
`kubectl delete -f deployment-demo.yaml`

--
### Daemonset

Создаем Daemonset:  
`kubectl apply -f daemonset-demo.yaml`

Смотрим количество Pod, запущенных Daemonset:  
`kubectl get po -n daemonset-demo -o wide`

Удаляем Daemonset:  
`kubectl delete -f daemonset-demo.yaml`

--
### Statefulset
Создаем Statefulset:  
`kubectl apply -f statefulset-demo.yaml`

Смотрим количество Pod, запущенных Statefulset:  
`kubectl get po -n statefulset-demo`

Изменяем количество Pod, запущенных Statefulset:  
`kubectl scale statefulset -n statefulset-demo  statefulset-demo --replicas=3`

Смотрим количество Pod, запущенных Statefulset:  
`kubectl get po -n statefulset-demo`

Удаляем Statefulset:  
`kubectl delete -f statefulset-demo.yaml`


