# Kubernetes
Manifeste de déploiement contenant :
- Une API FastAPI
- Une base de donnée MySQL

Ce manifeste de déploiement:
- Crée 3 Pods, chacun de ces Pods contenant à la fois un container MySQL et un container FastAPI. 
- Un service pour exposer l'API
- Un Ingress pour permettre l'accès à l'API.
