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

#### Проверяем, что от созданного пользователя мы можем запустить например pod  
```
kubectl --kubeconfig=demo-user.kubeconfig  run -n demo-rbac-ns --generator=run-pod/v1 tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
kubectl --kubeconfig=demo-user.kubeconfig  run -n monitoring --generator=run-pod/v1 tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash  
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
