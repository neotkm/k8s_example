## Практика
```
kubectl apply -f demo-pod-log-1.yaml

kubectl logs -n demo-pod-log-1 demo-pod-log-1
kubectl logs -n demo-pod-log-1 demo-pod-log-1 --timestamps=true
kubectl logs -f -n demo-pod-log-1 demo-pod-log-1 --timestamps=true

kubectl logs --tail=10 -n demo-pod-log-1 demo-pod-log-1 --timestamps=true
kubectl logs --tail=20 -n demo-pod-log-1 demo-pod-log-1 --timestamps=true
kubectl logs -f --tail=20 -n demo-pod-log-1 demo-pod-log-1 --timestamps=true


kubectl apply -f demo-pod-log-2.yaml
kubectl logs -f -n demo-pod-log-2 demo-pod-log-2
kubectl logs -f -n demo-pod-log-2 demo-pod-log-2 -c count1
kubectl logs -f -n demo-pod-log-2 demo-pod-log-2 -c count2


kubectl logs -n demo-pod-log-1 demo-pod-log-1 --since-time=2020-07-15T16:51:52Z --timestamps=true | less
kubectl logs -n demo-pod-log-1 demo-pod-log-1 --since-time=2020-07-15T16:51:52+00:00 --timestamps=true | less
kubectl logs -n demo-pod-log-1 demo-pod-log-1 --since-time=2020-07-15T16:51:52+03:00 --timestamps=true | less
kubectl logs -n demo-pod-log-1 demo-pod-log-1 --since-time=2020-07-15T16:51:52-04:00 --timestamps=true | less
see https://www.ietf.org/rfc/rfc3339.txt

kubectl logs -n demo-pod-log-1 demo-pod-log-1 --since=1h
kubectl logs -n demo-pod-log-1 demo-pod-log-1 --since=10s --timestamps=true

kubectl apply -f demo-pod-log-3.yaml
kubectl logs -f -n demo-pod-log-3        demo-pod-log-3 count2
kubectl logs -f -n demo-pod-log-3        demo-pod-log-3 count2 -p

kubectl apply -f demo-pod-log-4.yaml
kubectl get po -n demo-pod-log-4 --show-labels
kubectl logs -f -n demo-pod-log-4 -lapp=demo-pod-log-4

kubectl logs -f -n demo-pod-log-4 -lapp=demo-pod-log-4-2
kubectl logs -f -n demo-pod-log-4 -lapp=demo-pod-log-4-2 -c count1
kubectl logs -f -n demo-pod-log-4 -lapp=demo-pod-log-4-2 -c count2
kubectl logs -f -n demo-pod-log-4 -lapp=demo-pod-log-4-2 --all-containers=true
kubectl logs -f -n demo-pod-log-4 -lapp=demo-pod-log-4-2 --all-containers=true --max-log-requests=10

```
