# Documentation

Documentation for microservices contained in the organisation.

To congfigure ingress:

```bash
kubectl apply -f ingress.yaml 
```

To connect to AKS to resource group **rsoGroup**, to cluster **rsoCluster**, use:

```bash
az aks get-credentials --resoource-group rsoGroup --name rsoCluster
```



## Running localy: prerequisites

Create network **rsonetwork** if there is none:

```bash
docker network create rsonetwork
```

Install MongoDB and start MongoDB server with:
```bash
mongod
```

Run mongo:
```bash
docker run --name=mongo --rm --network=rsonetwork mongo
```



## Admin microservice

### Prerequisites


Clean install Maven package:
```bash
mvn clean install
```


## Build and run Docker containers

Building **admin-service** container:
```bash
docker build -t admin-service .
```

Run app:
```bash
docker run --name=admin-service --rm --network=rsonetwork -p 8080:8080 -e MONGO_USER_URL=MONGO_URL=mongodb://mongo:27017/users admin-service
```

## Rename Docker image and push to repository

Rename image:
```bash
docker tag admin-service rsoreg.azurecr.io/admin-service:**tag**
```

Push to repositorty:
```bash
docker push rsoreg.azurecr.io/admin-service:**tag**
```


### Deploy to Kubernetes

In repository kube-repo:

```bash
cd kube
```
```bash
kubectl apply -f admin-service.yaml 
```

Make sure to select the right tag for the image to be used.



## User microservice

### Prerequisites


Clean install Maven package:
```bash
mvn clean install
```


## Build and run Docker containers

Building **user-service** container:
```bash
docker build -t user-service .
```

Run app:
```bash
docker run --name=user-service --rm --network=rsonetwork -p 8080:8080 -e MONGO_USER_URL=MONGO_URL=mongodb://mongo:27017/users user-service
```

## Rename Docker image and push to repository

Rename image:
```bash
docker tag user-service rsoreg.azurecr.io/user-service:**tag**
```

Push to repositorty:
```bash
docker push rsoreg.azurecr.io/user-service:**tag**
```


### Deploy to Kubernetes

In repository kube-repo:

```bash
cd kube
```
```bash
kubectl apply -f user-service.yaml 
```
```bash
kubectl apply -f shared-service-mongo.yaml 
```

Make sure to select the right tag for the image to be used.



## Order microservice

### Prerequisites


Clean install Maven package:
```bash
mvn clean install
```


## Build and run Docker containers

Building **order-service1** container:
```bash
docker build -t order-service1 .
```

Run app:
```bash
docker run --name=order-service1 --rm --network=rsonetwork -p 8080:8080 -e MONGO_ORDER_URL=MONGO_URL=mongodb://mongo:27017/dev user-service1
```

## Rename Docker image and push to repository

Rename image:
```bash
docker tag order-service1 rsoreg.azurecr.io/order-service:**tag**
```

Push to repositorty:
```bash
docker push rsoreg.azurecr.io/order-service:**tag**
```


### Deploy to Kubernetes

In repository kube-repo:

```bash
cd kube
```
```bash
kubectl apply -f order-service.yaml 
```
```bash
kubectl apply -f order-service-mongo.yaml 
```

Make sure to select the right tag for the image to be used.

## Product microservice

### Prerequisites


Clean install Maven package:
```bash
mvn clean install
```


## Build and run Docker containers

Building **productservice** container:
```bash
docker build -t productservice .
```

Run app:
```bash
docker run --name=productservice --rm --network=rsonetwork -p 8080:8080 -e MONGO_PRODUCTS_URL=MONGO_URL=mongodb://mongo:27017/products productservice
```

## Rename Docker image and push to repository

Rename image:
```bash
docker tag productservice rsoreg.azurecr.io/product-service:**tag**
```

Push to repositorty:
```bash
docker push rsoreg.azurecr.io/order-product:**tag**
```


### Deploy to Kubernetes

In repository kube-repo:

```bash
cd kube
```
```bash
kubectl apply -f product-service.yaml 
```
```bash
kubectl apply -f product-service-mongo.yaml 
```

Make sure to select the right tag for the image to be used.


### Ingress

In repository kube-repo:

```bash
cd ingress
```
```bash
helm upgrade ingress-nginx ingress-nginx/ingress-nginx \
  --create-namespace \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz
```
```bash
kubectl apply -f ingress.yaml --namespace ingress-basic 
```
