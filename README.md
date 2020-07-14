## Демо к вводному курсу по Kubernetes  

### Клонируем репозиторий:  
`git clone https://github.com/neotkm/k8s_example.git`  

### Получение токена для авторизации в Kubernetes UI:  
`kubectl -n kube-systems describe secret $(kubectl -n kube-systems get secret | grep admin-user | awk '{print $1}')`  

### Демо к занятию по архитектуре и основным сущностям:  
[controller-kub3](/controller-kub3)  

### Демо к занятию по configmap / secret:  
[configuration-kub3](/configuration-kub3)  

### Демо к занятию по RBAC:  
[rbac-kub3](/rbac-kub3)  

### Демо к занятию по управлению ресурсами:  
[resource_management-kub3](/resource_management-kub3)  

### Демо к занятию по сети / сервисам:  
[service-kub3](/service-kub3)    

### Демо к занятию по PV / PVC:  
[volume-kub3](/volume-kub3)  

### Демо к занятию по мониторингу:  
[monitoring-kub3](/monitoring-kub3)  

### Демо к занятию по логированию:  
[logging-kub3](/logging-kub3)  


