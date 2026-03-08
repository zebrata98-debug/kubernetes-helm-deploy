# kubernetes-helm-deploy

A personal project to get hands-on with Kubernetes and Helm. I wanted to 
understand how real deployments work beyond just running containers with 
Docker — things like health checks, resource limits, and what happens when 
you roll out a bad update.

Everything runs locally using Minikube so there's no cloud cost involved.

## What it does

Deploys a simple Node.js app to a local Kubernetes cluster with:

- 2 replicas running at all times
- Liveness and readiness probes so Kubernetes knows if the app is healthy
- CPU and memory limits so the app can't consume all available resources
- Horizontal Pod Autoscaler that scales up to 5 replicas under CPU load
- Rolling updates so new versions deploy without downtime
- One-command rollback if something goes wrong

## Project layout
```
kubernetes-helm-deploy/
├── app/
│   ├── index.js           
│   ├── package.json
│   └── Dockerfile
├── helm/
│   └── myapp/
│       ├── Chart.yaml     
│       ├── values.yaml    
│       └── templates/
│           ├── deployment.yaml   
│           ├── service.yaml      
│           └── hpa.yaml          
└── README.md
```


minikube start --driver=docker

eval $(minikube docker-env)

docker build -t myapp:1.0 app/

helm install myapp-release helm/myapp

kubectl get pods

kubectl get services

kubectl get hpa

minikube service myapp-release-myapp --url

docker build -t myapp:2.0 app/

helm upgrade myapp-release helm/myapp --set image.tag=2.0

kubectl rollout status deployment/myapp-release-myapp

helm rollback myapp-release 1

helm uninstall myapp-release
minikube stop

