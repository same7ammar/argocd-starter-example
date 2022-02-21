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

## 4.Deploy a Sample app using Argocd .
```sh 
git clone https://github.com/same7ammar/argocd-starter-example.git
kubeclt apply -f application.yml
application.argoproj.io/first-argo-application created
```
check the app in Argocd dashborad .

![Alt text](files/2.PNG?raw=true "first-argo-application")

```sh
$ kubectl get pods -n web-app
NAME                              READY   STATUS    RESTARTS   AGE
nodejs-web-app-6fdf6d4f54-7drlz   1/1     Running   0          29m

$ kubectl get svc
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
nodejs-web-app   LoadBalancer   10.43.240.254   172.17.152.146   3000:30043/TCP   7s
to test service use  curl http://EXTERNAL-IP:PORT
$ curl http://172.17.152.146 
Hello World

```

## Build  a simple pipeline .
1. create a github repo under your user name .
2. copy application.yml and app folder to your repo .
3. edit application.yml .
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: first-argo-application
  namespace: argocd
spec:
  project: default

  source:
    repoURL:  https://yourgitrepo
    targetRevision: HEAD
    path: app # mainfest folder in your repo
  destination: 
    server: https://kubernetes.default.svc
    namespace: web-app  # namespace for your app .
```
4. deploy application.yml using kubectl .
```sh
kubectl apply  -f application.yml
```
5. push a new version of your app ie. edit 
```yaml
      containers:
        - name: nodejs-web-app
          image: same7ammar/node-web-app-k8s:v1
```
Check your argocd dashborad , new version show be avaiable .


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)