# AAA & RBAC

## Kubernetes User
```
mkdir -p .certs
```
### generate key
```
openssl genrsa -out .certs/sampleuser.key 2048
```
### generate cert request
```
openssl req -new -key .certs/sampleuser.key -out .certs/sampleuser.csr -subj "/CN=sample/O=epam"
```

### issue certificate
```
scp -i $(minikube ssh-key) .certs/sampleuser.csr docker@$(minikube ip):/home/docker
minikube ssh
sudo -i
openssl x509 -req -in /home/docker/sampleuser.csr -CA /var/lib/minikube/certs/ca.crt -CAkey /var/lib/minikube/certs/ca.key -CAcreateserial -out /home/docker/sampleuser.crt -days 500
exit
exit
scp -i $(minikube ssh-key) docker@$(minikube ip):/home/docker/sampleuser.crt .certs/
```

### add user into config file
```
kubectl config set-credentials sampleuser --client-certificate="$(pwd)/.certs/sampleuser.crt"  --client-key="$(pwd)/.certs/sampleuser.key"
```

### create context
```
kubectl config set-context sampleuser-context --cluster=minikube --user=sampleuser
```

### set context as default
```
kubectl auth can-i get pods -n default
kubectl config use-context sampleuser-context
kubectl auth can-i get pods -n default
```
### revert back
```
kubectl config use-context minikube
```

## Service account
### Create
```
kubectl create sa service --dry-run=client -o yaml > sa.yaml
kubectl apply -f sa.yaml
kubectl get sa service
```

### binding
```
kubectl auth can-i get pods -n default --as system:serviceaccount:default:service
kubectl create rolebinding service-binding --clusterrole=cluster-admin --serviceaccount=default:service
kubectl auth can-i get pods -n default --as system:serviceaccount:default:service
kubectl auth can-i get pods -n kube-system --as system:serviceaccount:default:service
```

### access API from inside pod
```
kubectl run web --image=nginx:latest
kubectl describe pod web
kubectl exec -it web -- bash
curl -k https://${KUBERNETES_SERVICE_HOST}/api
curl --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt --header "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" -X GET https://${KUBERNETES_SERVICE_HOST}/api
```

## apply cluster role binding to user
```
kubectl apply -f sample_clusterrolebinding.yaml
kubectl config use-context sampleuser-context
kubectl auth can-i get pods -n default
```

## Cluster settings in GKE
```
kubectl get pods -A
kubectl cluster-info dump | grep authorization-mode
less .kube/config
```

# GKE
```
kubectl create deployment web --image=nginx:latest
```
Query
> resource.type="k8s_cluster"
> protoPayload.methodName:"deployments.create"


# Admission Controllers
```
kubectl delete namespace kube-public
minikube ssh
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```
add command option and remove plugin from enabled:
```
- --disable-admission-plugins=NamespaceLifecycle
```
```
kubectl delete namespace kube-public
```
