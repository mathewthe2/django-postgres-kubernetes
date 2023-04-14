# django-postgres-kubernetes
skeleton for django and postgres on kubernetes

## Task 1: Hello World with Minikube

### Installation
#### Docker
https://www.docker.com/products/docker-desktop/
#### Minikube
https://minikube.sigs.k8s.io/docs/start/

Mac: `$ brew install minikube`

Windows: `$ choco install minikube`

Linux:
```bash
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Config kubectl and docker
https://minikube.sigs.k8s.io/docs/handbook/kubectl/

```bash
$ alias kubectl="minikube kubectl --"
$ eval $(minikube docker-env)
# Validate 
$ docker info | grep Name
Name: minikube
```

### Deploy hello-world app
```bash
$ k create deployment web --image=gcr.io/google-samples/hello-app:1.0

deployment.apps/web created

$ k expose deployment web --type=NodePort --port=8080

service/web exposed

$ minikube service web
```
### Check dashboard
`$ minikube dashboard`

## Task 2: Canary Deployment of a Django app with PostgresDB

### Setup
### Clone repo
`$ git clone https://github.com/mathewthe2/django-postgres-kubernetes.git`

### Setup
Build docker image for Django app

`$ docker build -t django-postgres:1.0.0 .`

Create PostgresDB

`$ k apply -f deploy/kubernetes/postgres/`

Create Django App

`$ k apply -f deploy/kubernetes/django/`

Confirm everything works

`$ minikube service django-service`

`$ minikube dashboard`

### Build new version
Edit urls.py to redirect homepage to green template.
`vim kubernetes_django/urls.py`

Build docker image
`docker build -t django-postgres:1.0.1 .`

Copy deployment file.
`$ cp deploy/kubernetes/django/deployment.yaml deploy/kubernetes/django/deployment-v2.yaml`

Edit both deployment files. 

Original file
- update replica size to 3

New deployment file
- new name: deployment_1.0.1
- keep replica size to 1

k apply -f delpoy/kubernetes/django

## Reference
https://github.com/gitumarkk/kubernetes_django
https://markgituma.medium.com/kubernetes-local-to-production-with-django-1-introduction-d73adc9ce4b4