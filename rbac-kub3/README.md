## Практика  
### RBAC  

#### Задаем переменые окружения  
```  
user=demo-user  
ns=demo-rbac-ns  
```

#### Генерируем сертификаты для пользователя  
```
openssl genrsa -out $user.key 2048
openssl req -new -key $user.key -out $user.csr -subj "/CN=$user"
```

#### Создаем запрос в кластер, для подписи сертификата  
```
cat <<EOF | kubectl create -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: user-$user-csr
spec:
  groups:
  - system:authenticated
  request: $(cat $user.csr | base64 | tr -d '\n')
  signerName: kubernetes.io/kube-apiserver-client  
  usages:
  - client auth
EOF
```

#### Проверяем что запрос есть и подтверждаем его  
```
kubectl get csr  
kubectl certificate approve user-$user-csr  
```

#### Получаем подписанный сертификат, создаем неймспейс и даем права пользователю в этом неймспейсе  
```
kubectl get csr user-$user-csr -o jsonpath='{.status.certificate}' | base64 --decode > $user.crt  
kubectl create namespace $ns  
kubectl create rolebinding $user-bind --clusterrole=cluster-admin --user=$user --namespace=$ns  
```

#### Генерируем kubeconfig  
```
c=`kubectl config current-context`
csn=`kubectl config get-contexts $c | awk '{print $3}' | tail -n 1`  
APISERVER=$(kubectl config view --minify | grep server | cut -f 2- -d ":" | tr -d " ")  
kubectl config set-cluster $csn --insecure-skip-tls-verify=true --server=$APISERVER --kubeconfig=$user.kubeconfig  
kubectl config set-credentials $user --client-certificate=$user.crt --client-key=$user.key --embed-certs=true --kubeconfig=$user.kubeconfig  
kubectl config set-context $c --cluster=$csn --user=$user --namespace=$ns --kubeconfig=$user.kubeconfig  
kubectl config use-context --kubeconfig=$user.kubeconfig $c  
```
#### Смотрим версию kubectl  
```
kubectl version

Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.0", GitCommit:"cb303e613a121a29364f75cc67d3d580833a7479", GitTreeState:"clean", BuildDate:"2021-04-08T16:31:21Z", GoVersion:"go1.16.1", Compiler:"gc", Platform:"darwin/arm64"}
Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.4", GitCommit:"e87da0bd6e03ec3fea7933c4b5263d151aafd07c", GitTreeState:"archive", BuildDate:"2021-02-19T10:59:12Z", GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"}
```

#### Проверяем, что от созданного пользователя мы можем запустить например pod  
##### если версия kubectl < v1.21 выполняем эти команды  
```
kubectl --kubeconfig=demo-user.kubeconfig  run -n demo-rbac-ns --generator=run-pod/v1 tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
kubectl --kubeconfig=demo-user.kubeconfig  run -n monitoring --generator=run-pod/v1 tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
```

##### если версия kubectl >= v1.21.* выполняем эти команды  
```
kubectl --kubeconfig=demo-user.kubeconfig  run -n demo-rbac-ns tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
kubectl --kubeconfig=demo-user.kubeconfig  run -n monitoring --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
```

#### Удаляем неймспейс и пользователя   
```  
kubectl delete csr user-$user-csr
kubectl delete namespace $ns
```  

```  
kubectl apply -f demo-namespace.yaml  
kubectl apply -f pod-read-create-role.yaml  
kubectl apply -f pod-read-create-rolebinding.yaml  
kubectl auth can-i get po --namespace rbac-demo --as developer  
kubectl auth can-i get po --namespace kube-system --as developer  
kubectl auth can-i delete po --namespace rbac-demo --as developer  
```  
