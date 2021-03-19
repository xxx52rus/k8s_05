# EmptyDir
```
kubectl apply -f pod_empty_dir.yaml
kubectl exec -it empty-dir -c web -- bash
```
```
echo 'some message' > /logs/message.log
exit
```
```
kubectl exec -it empty-dir -c process -- cat /process/message.log
```

# hostPath
```
kubectl get pod -n kube-system kube-apiserver-minikube -o yaml
```

# PV & PVC
```
kubectl apply -f pv_host_path.yaml
kubectl get pv
kubectl apply -f pvc_host_path.yaml
kubectl get pv
kubectl get pvc
kubectl apply -f pod_with_pvc.yaml
```

```
kubectl exec -it pv-pod pv-pod -- bash
echo "hello world" > /usr/share/nginx/html/default.html
exit
```
```
kubectl delete pod pv-pod
kubectl apply -f pod_with_pvc.yaml
kubectl exec -it pv-pod -- cat /usr/share/nginx/html/default.html
```
```
minikube ssh
cat /storage/data/default.html
exit
```


# Dynamic PVC in GKE
```
kubectl get storageclass
kubectl apply -f pod_pvc_gke.yaml
```

# StatefulSet
```
kubectl apply -f stateful_set.yaml
kubectl get pods -w
```
```
kubectl exec -it service-0 -- bash
apt-get update && apt-get install -y dnsutils
nslookup nginx
exit
```
