# Kubernetes
Manifeste de déploiement contenant :
- Une API FastAPI
- Une base de donnée MySQL

Ce manifeste de déploiement:
- Crée 3 Pods, chacun de ces Pods contenant à la fois un container MySQL et un container FastAPI. 
- Un service pour exposer l'API
- Un Ingress pour permettre l'accès à l'API.

# Mise en place
```

# config-map
kubectl create -f my-config-map.yml
#Pour verifier la création du config-map
kubectl get configmap |grep my-config-map

# secret
kubectl create -f my-secret.yml 
#Pour verifier la création du secret
kubectl get secret |grep my-secret

# deployment
kubectl create -f my-deployment.yml
#Pour verifier la création du deploiement
kubectl get deployment |grep my-deployment

# service
kubectl create -f my-service.yml
#Pour verifier la création du service
kubectl get service |grep my-service

# Ingress
kubectl create -f my-ingress.yml
#Pour verifier la création de l'ingress
kubectl get ingress |grep my-ingress

```
