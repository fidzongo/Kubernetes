# Manifeste de deploiement d'une api
Manifeste de déploiement contenant :
- Une API FastAPI
- Une base de donnée MySQL

Ce manifeste de déploiement:
- Crée 3 Pods, chacun de ces Pods contenant à la fois un container MySQL et un container FastAPI. 
- Un service pour exposer l'API
- Un Ingress pour permettre l'accès à l'API.

# Création de l'api
fichier main.py:
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from sqlalchemy.engine import create_engine

# Custom add
# Import de la librairie os pour import des variables d'environnements
import os
# Pour lever l'exception ObjectNotExecutableError
from sqlalchemy import text
import pymysql
pymysql.install_as_MySQLdb()

# creating a FastAPI server
server = FastAPI(title='User API')

# creating a connection to the database
# On recupère la valeur a partir de la variable d'environnement MYSQL_URL
mysql_url = os.environ["MYSQL_URL"]
mysql_user = 'user_mysql'
# On recupère la valeur a partir de la variable d'environnement MYSQL_PASSWORD
mysql_password = os.environ["MYSQL_PASSWORD"]
database_name = 'Main'

# recreating the URL connection
connection_url = 'mysql://{user}:{password}@{url}/{database}'.format(
    user=mysql_user,
    password=mysql_password,
    url=mysql_url,
    database=database_name
)

# creating the connection
mysql_engine = create_engine(connection_url)


# creating a User class
class User(BaseModel):
    user_id: int = 0
    username: str = 'daniel'
    email: str = 'daniel@datascientest.com'


@server.get('/status')
async def get_status():
    """Returns 1
    """
    return 1


@server.get('/users')
async def get_users():
    with mysql_engine.connect() as connection:
        results = connection.execute(text('SELECT * FROM Users;'))

    results = [
        User(
            user_id=i[0],
            username=i[1],
            email=i[2]
            ) for i in results.fetchall()]
    return results


@server.get('/users/{user_id:int}', response_model=User)
async def get_user(user_id):
    with mysql_engine.connect() as connection:
        results = connection.execute(text(
            'SELECT * FROM Users WHERE Users.id = {};'.format(user_id)))

    results = [
        User(
            user_id=i[0],
            username=i[1],
            email=i[2]
            ) for i in results.fetchall()]

    if len(results) == 0:
        raise HTTPException(
            status_code=404,
            detail='Unknown User ID')
    else:
        return results[0]
```

# Création de l'image docker de l'api
On considerera ici que la base de donnée mysql est déjà installée (les credentials dans le fichiers credentials.env)
```
# Construction à partir du dockerfile
mkir fastapi
cp requirements.txt fastapi/
cp Dockerfile fastapi/
docker image build . -t fast-api:1.0.0

ou
# Recuperation à partir du docker hub
docker pull fidzongo/fast-api:1.0.0

# Test unitaire de l'api
docker container run --rm -p 8000:8000 --env-file credentials.env --network my_network fidzongo/fast-api:1.0.0
```

# Déploiement de l'application
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
