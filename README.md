# About 
Simple example - How to install and configure Argocd in kubernetes cluster and deploy sample nodejs app.

## requirements 
1. Docker Installed.
2. Rancher Desktop - https://rancherdesktop.io .
3. kubectl if not installed .

## 1.Install Argo CD into K8s cluster .
```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
##  Kubernetes manifests can be :
- kustomize applications
- helm charts
- ksonnet applications
- jsonnet files
- Plain directory of YAML/json manifests
- Any custom config management tool configured as a config management plugin

in this guide i will use Kubernetes mainfest files . 

## 2.Access Argocd Dashborad via Port Forwarding.
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
The API server can then be accessed using the http://localhost:8080 

## 3.Get argocd Dashborad password .
```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
OFj9zupMWoCUu6nu
```

![Alt text](files/1.PNG?raw=true "Argocd Dashborad")

## 4.Deploy Sample .
