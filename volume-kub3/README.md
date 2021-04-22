## Практика
### Volume: EmptyDir  
### Volume: HostPath   
### Volume: Local  
### Volume: PVC & StatefulSet  

--
### Volume: EmptyDir 

```
kubectl apply -f demo-emptydir-volume.yaml  
kubectl exec -it demo-emptydir-volume  -c demo-emptydir-volume -- sh  
df -h  
echo "test_data" > /data/emptydir/test.txt  
cat /data/emptydir/test.txt  
kubectl exec -it demo-emptydir-volume  -c demo-emptydir-volume-2 -- sh  
ls -lh /data/emptydir/  
kubectl delete -f demo-emptydir-volume.yaml  
kubectl apply -f demo-emptydir-volume.yaml  
kubectl exec -it demo-emptydir-volume  -c demo-emptydir-volume -- sh  
ls -lh /data/emptydir/  
kubectl delete -f demo-emptydir-volume.yaml

kubectl apply -f demo-emptydir-volume-ram.yaml
kubectl describe po -n default demo-emptydir-volume
kubectl describe po -n default demo-emptydir-volume-ram
kubectl delete -f demo-emptydir-volume-ram.yaml  
```  

--
### Volume: HostPath  

```
kubectl apply -f demo-hostpath-volume.yaml
kubectl get po
kubectl describe po -n default demo-hostpath-volume
kubectl --kubeconfig=config delete -f demo-hostpath-volume.yaml
kubectl exec -it demo-hostpath-volume sh  
ls -lh /test-pd/  
echo "test_data" > /test-pd/test-pd.txt  
kubectl delete -f  demo-hostpath-volume.yaml  
kubectl apply -f demo-hostpath-volume.yaml  
kubectl exec -it demo-hostpath-volume sh  
ls -lh /test-pd/  
kubectl delete -f  demo-hostpath-volume.yaml
```

--
### Volume: Local   
```
выбираем любой воркер, его имя указано в первом столбце

kubectl get nodes

у меня так, у Вас само собой будут другие:
NAME        STATUS   ROLES    AGE   VERSION
test21-s1   Ready    <none>   38d   v1.20.4
test21-s2   Ready    <none>   38d   v1.20.4
test21-s3   Ready    <none>   38d   v1.20.4
test21-s4   Ready    <none>   38d   v1.20.4
test21-s5   Ready    <none>   38d   v1.20.4


kubectl apply -f demo-local-volume-pv.yaml
kubectl get pv
kubectl describe pv local-pv

kubectl apply -f demo-local-storage-class.yaml
kubectl get sc
kubectl describe sc local-storage

kubectl apply -f demo-local-volume-pvc.yaml
kubectl get pvc
kubectl describe pvc local-pvc


kubectl apply -f demo-local-volume-pod.yaml
kubectl describe po demo-local-volume-pod

kubectl exec -it demo-local-volume-pod -- sh  
echo "test" > /test-pv/test.txt  
cat /test-pv/test.txt  
exit  

kubectl delete -f demo-local-volume-pod.yaml  
kubectl apply -f demo-local-volume-pod.yaml  
kubectl get po -o wide  

kubectl exec -it demo-local-volume-pod -- sh  
cat /test-pv/test.txt  

kubectl delete -f demo-local-volume-pod.yaml  
kubectl delete -f demo-local-volume-pvc.yaml  
kubectl get pv  
kubectl describe pv local-pv  
kubectl delete -f demo-local-volume-pv.yaml  
```  

--
### Volume: PVC & StatefulSet    
```
kubectl apply -f statefulset-demo-pvc.yaml
kubectl get po -n statefulset-demo-pvc
kubectl get pv
kubectl get pvc -n statefulset-demo-pvc
kubectl delete -f statefulset-demo-pvc.yaml

```

