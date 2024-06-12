# k3d-demo

A demo environment for doing K8s development. Details on how this repo was setup is here:

* [Setup](docs/setup.md)

## Quick start

Create a Docker registry and an associated k3s cluster

```
k3d registry create myregistry.localhost --port 5000
k3d cluster create --registry-use k3d-myregistry.localhost:5000 --api-port 6550 -p "8081:80@loadbalancer" --agents 2
```

Build + push application

```
docker buildx build -t k3d-myregistry.localhost:5000/demo:v0.1 . --push
```

Deploy application

```
kubectl create namespace demo
kubectl create deployment demo --image=k3d-myregistry.localhost:5000/demo:v0.1 --replicas=1 --namespace demo
kubectl expose deployment demo --port 80 --target-port=8080 --namespace demo
kubectl create ingress demo --class=traefik --rule=demo.localhost/*=demo:80 --namespace demo
```

Access the application

* http://demo.localhost:8081

## Cleanup

```
k3d cluster delete k3s-default
k3d registry delete myregistry.localhost
```
