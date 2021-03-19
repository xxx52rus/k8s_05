# Nginx ingress
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```
## Retrieve list of charts from repo
```
helm search repo ingress-nginx
```
## Retrieve values from helm chart
```
helm show values ingress-nginx/ingress-nginx > values.yaml
```
```
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --wait --values=values.yaml --version=3.7.1 --namespace=ingress --create-namespace
```
## List what we have installed
```
helm list -n ingress
```
# Create Helm Chart
```
helm create app
```
```
rm app/values.yaml
rm templates/*
```
Copy application manifests in app/templates
## install the app
```
helm upgrade --install app app --wait --namespace=app --create-namespace
```
```
kubectl get deploy,rs,pod,svc,ep,ingress -n app
helm list -n app
```
```
helm history app -n app
helm rollback app -n app 1
helm history app -n app
helm rollback app -n app 3
```
