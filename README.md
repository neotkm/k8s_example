
Получение токена для авторизации в Kubernetes UI:  
`kubectl -n kube-systems describe secret $(kubectl -n kube-systems get secret | grep admin-user | awk '{print $1}')`  

Демо к занятию по архитектуре и основным сущностям:  
[controller-kub3](/controller-kub3)  

