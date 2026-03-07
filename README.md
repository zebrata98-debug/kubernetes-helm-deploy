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
│   ├── index.js           # Simple Node.js HTTP server
│   ├── package.json
│   └── Dockerfile
├── helm/
│   └── myapp/
│       ├── Chart.yaml     # Helm chart metadata
│       ├── values.yaml    # Configurable values
│       └── templates/
│           ├── deployment.yaml   # Kubernetes deployment
│           ├── service.yaml      # Exposes the app
│           └── hpa.yaml          # Autoscaling config
└── README.md
```

## How to run it

You'll need Minikube, kubectl, Helm, and Docker installed.
```bash
# Start the cluster
minikube start --driver=docker

# Point Docker at Minikube's daemon
eval $(minikube docker-env)

# Build the app image
docker build -t myapp:1.0 app/

# Deploy with Helm
helm install myapp-release helm/myapp

# Check everything is running
kubectl get pods
kubectl get services
kubectl get hpa
```

Get the URL to access the app:
```bash
minikube service myapp-release-myapp --url
```

## Deploying an update
```bash
# Build the new version
docker build -t myapp:2.0 app/

# Upgrade the release
helm upgrade myapp-release helm/myapp --set image.tag=2.0

# Watch the rolling update
kubectl rollout status deployment/myapp-release-myapp
```

## Rolling back
```bash
helm rollback myapp-release 1
```

## Cleanup
```bash
helm uninstall myapp-release
minikube stop
```

## What I learned

- How Helm templates work and why they're useful over raw Kubernetes manifests
- The difference between liveness and readiness probes and why both matter
- How resource requests and limits affect pod scheduling
- How the HPA decides when to scale and what metrics it uses
- How rolling updates work under the hood — Kubernetes spins up new pods 
  before terminating old ones
- How to roll back a release instantly with a single Helm command
