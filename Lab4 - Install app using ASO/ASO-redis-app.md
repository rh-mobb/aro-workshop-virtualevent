## Deploy Azure Service Operator

Azure Service Operator (ASO) is an open-source project by Microsoft Azure. ASO gives you the ability to provision and manage Azure resources such as compute, databases, resource groups, networking, etc. as objects in Kubernetes using declarative Kubernetes manifests.

**!!! Warning**
    Azure Service Operator is in its second incarnation (v2) and is in Tech Preview, this means it is not fully operizationalized into Operator Hub and should not be used for Production. However it's really nifty and we like it.

ASO consists of:

- Custom Resource Definitions (CRDs) for each of the Azure services that a Kubernetes user can provision.
- A Kubernetes controller that manages the Azure resources represented by the user-specified Custom Resources. The controller attempts to synchronize the desired state in the user-specified Custom Resource with the actual state of that resource in Azure, creating it if it doesn't exist, updating it if it has been changed, or deleting it.

![Azure-Service-operator](/Images/Lab4-aso-schematic.png)

## Azure Services Overview

Applications running on ARO often use other Azure services like databases, caching, message queues or storage. Using ASO, these services can be managed directly inside the cluster. In this task we will deploy an Azure Cache for Redis database that can be used by an application running on OpenShift. Azure Cache for Redis is a fully managed, in-memory cache that enables high-performance and scalable architectures.

To proceed with this task you will need to have completed the **Deploy Azure Service Operator** (already provisioned).

The Azure Voting App that will be deployed consists of a front end web-app that uses an Azure Cache for Redis instance to provide persistence of votes received for Cats and Dogs. The application interface has been built using Python / Flask.

#### Estimate time
Completing this tutorial should take about 15 minutes.

### Create a project to use for the application

OpenShift uses Projects to separate application resources on the cluster. Create a project for the Azure Voting App: Use a different project name **userid-redis-demo**

```bash
oc new-project <USERID>-redis-demo
```

### Set Environment variables

REDIS_PROJECT=<USERID>-redis-demo

REDIS_HOSTNAME=redis-wzlol9ei

Azure cache for Redis resource on Azure Portal (already setup for the lab)

![screenshot of azure portal showing redis](/Images/Lab4-azr-portal-redis.png)

### Deploy the Azure Voting App

The Azure Voting App will be deployed from a pre-built container that is stored in the public Microsoft Azure Container Registry. It's environment variables are configured to use the URL of the Redis cache deployed in the last step, and a Kubernetes Secret that was created as part of the cache deployment.

The application is exposed internal to the cluster using a service on port 80, and is exposed externally using an OpenShift Route with TLS termination through a public facing Azure Load Balancer.

**NOTE:**Deployment code with variable will fail, if environment variavle value is not setup for REDIS_PROJECT;

```bash
cat <<EOF | oc -n "${REDIS_PROJECT}" apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: aroworkshop.azurecr.io/azure-vote:latest
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 8080
        env:
        - name: REDIS
          valueFrom:
            secretKeyRef:
              name: redis-secret
              key: hostName
        - name: REDIS_NAME
          value: "${REDIS_HOSTNAME}"
        - name: REDIS_PWD
          valueFrom:
            secretKeyRef:
              name: redis-secret
              key: primaryKey
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  ports:
  - port: 8080
    targetPort: 8080
  selector:
    app: azure-vote-front
---
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: azure-vote
spec:
  port:
    targetPort: 8080
  to:
    kind: Service
    name: azure-vote-front
    weight: 100
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  wildcardPolicy: None
EOF
```
### Check to see if pods are running

```bash
oc get pods
```
You should see the following

```
$ oc get pods
 NAME                                READY   STATUS                       RESTARTS   AGE
azure-vote-front-69955487b4-zdbgc   0/1     CreateContainerConfigError   0          24s
```

The error is because it is looking for secrets to connect to Azure Cache for Redis. To fix this run the following command 

```
oc create secret generic redis-secret --from-literal=hostName=redis-wzlol9ei.redis.cache.windows.net \
--from-literal=port=6379 \
--from-literal=primaryKey=Skg0kOzNQlTW7KCdnvzsB9kO5PoPD2WpSAzCaPIaRxk= \
--from-literal=secondaryKey=EK5EqB7AI8asZDUvf6CV4c1wILlpWRzofAzCaL5yCxw=
```
You should see that secret/redis-secret created

### Access the Application

```bash
oc get route
```

Browse to the URL provided by the previous command and validate that the app is working

![screenshot of the voting app](/Images/Lab4-aro-voting-app.png)
