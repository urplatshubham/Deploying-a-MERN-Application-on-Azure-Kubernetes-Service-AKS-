# Deploying a MERN Application on Azure Kubernetes Service (AKS)

This document outlines the steps to deploy the [SampleMERNwithMicroservices](https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices) application on Azure Kubernetes Service (AKS). The steps include prerequisites, deployment configurations, and testing the deployment. Screenshots of the process are provided where necessary.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Cloning the Application Repository](#cloning-the-application-repository)
3. [Setting Up Azure Kubernetes Service (AKS)](#setting-up-azure-kubernetes-service-aks)
4. [Containerizing the Application](#containerizing-the-application)
5. [Creating Kubernetes Manifests](#creating-kubernetes-manifests)
6. [Deploying to AKS](#deploying-to-aks)
7. [Testing the Deployment](#testing-the-deployment)
8. [Documentation](#documentation)

---

## Prerequisites

Before starting, ensure you have the following:

- An Azure account with sufficient permissions to create resources.
- Azure CLI installed on your machine.
- Docker installed and configured.
- kubectl CLI installed and configured.
- Access to the [SampleMERNwithMicroservices](https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices) GitHub repository.
- Basic understanding of Docker, Kubernetes, and Azure.

---

## 1. Cloning the Application Repository

Clone the application repository to your local machine:

```bash
git clone https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices.git
cd SampleMERNwithMicroservices
```

![Screenshot of the cloned repository](path/to/screenshot-cloned-repo.png)

---

## 2. Setting Up Azure Kubernetes Service (AKS)

1. Log in to your Azure account:

   ```bash
   az login
   ```

   ![Screenshot of successful Azure login](path/to/screenshot-azure-login.png)

2. Create a resource group:

   ```bash
   az group create --name MERNAppResourceGroup --location eastus
   ```

   ![Screenshot of resource group creation](path/to/screenshot-resource-group.png)

3. Create an AKS cluster:

   ```bash
   az aks create --resource-group MERNAppResourceGroup --name MERNAppAKSCluster --node-count 2 --enable-addons monitoring --generate-ssh-keys
   ```

   ![Screenshot of AKS cluster creation](path/to/screenshot-aks-cluster.png)

4. Configure kubectl to connect to the AKS cluster:

   ```bash
   az aks get-credentials --resource-group MERNAppResourceGroup --name MERNAppAKSCluster
   ```

   Verify the connection to the cluster:

   ```bash
   kubectl get nodes
   ```

   ![Screenshot of kubectl connected to the AKS cluster](path/to/screenshot-kubectl-nodes.png)

---

## 3. Containerizing the Application

1. Create Dockerfiles for the frontend and backend components.

2. Build Docker images for each service:

   ```bash
   docker build -t frontend ./frontend
   docker build -t backend ./backend
   ```

3. Tag and push the images to Azure Container Registry (ACR):

   ```bash
   docker tag frontend <ACR_NAME>.azurecr.io/frontend
   docker tag backend <ACR_NAME>.azurecr.io/backend

   docker push <ACR_NAME>.azurecr.io/frontend
   docker push <ACR_NAME>.azurecr.io/backend
   ```

   - The `mern-frontend:latest` Docker image was successfully built and pushed to ACR. (Refer to the first screenshot.)
   - The `mern-profile-service:latest` Docker image was successfully built and pushed to ACR. (Refer to the second screenshot.)
   - The `mern-hello-service:latest` Docker image was successfully built and pushed to ACR. (Refer to the third screenshot.)

   ![Frontend Docker image pushed to ACR](WhatsApp%20Image%202025-01-18%20at%2010.33.50%20AM%20(1).jpeg)
   ![Profile Service Docker image pushed to ACR](WhatsApp%20Image%202025-01-18%20at%2010.33.50%20AM.jpeg)
   ![Hello Service Docker image pushed to ACR](WhatsApp%20Image%202025-01-18%20at%2010.33.51%20AM.jpeg)

---

## 4. Creating Kubernetes Manifests

Create Kubernetes manifests for deployment and service configurations:

- `frontend-deployment.yaml`
- `frontend-service.yaml`
- `backend-deployment.yaml`
- `backend-service.yaml`

Example for a deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: <ACR_NAME>.azurecr.io/frontend
        ports:
        - containerPort: 3000
```

---

## 5. Deploying to AKS

1. Apply the manifests to the AKS cluster:

   ```bash
   kubectl apply -f frontend-deployment.yaml
   kubectl apply -f frontend-service.yaml
   kubectl apply -f backend-deployment.yaml
   kubectl apply -f backend-service.yaml
   ```

2. Verify the deployment:

   ```bash
   kubectl get all
   ```

   - The `mern-profile-service` was successfully deployed. (Refer to the fourth screenshot.)
   - The `mern-hello-service` was successfully deployed. (Refer to the fifth screenshot.)

   ![Profile service deployment applied](WhatsApp%20Image%202025-01-18%20at%2010.33.51%20AM%20(1).jpeg)
   ![Hello service deployment applied](WhatsApp%20Image%202025-01-18%20at%2010.33.51%20AM%20(2).jpeg)

---

## 6. Testing the Deployment

1. Obtain the external IP of the frontend service:

   ```bash
   kubectl get service frontend
   ```

   ![Screenshot of frontend service external IP](path/to/screenshot-frontend-service.png)

2. Access the application in a web browser using the external IP.

   ![Screenshot of the running application](path/to/screenshot-running-application.png)

---

## 7. Documentation

1. Take screenshots of the following steps:
   - AKS cluster creation in Azure Portal.
   - Docker images built and pushed to ACR.
   - Kubernetes manifests applied.
   - Application running in the browser.

2. Push all files, including the Kubernetes manifests and screenshots, to a GitHub repository.

---

## Submission

1. Push your code and documentation to a GitHub repository.
2. Share the repository link in a Word or PDF file.
3. Submit the file containing the repository link on Vlearn.
