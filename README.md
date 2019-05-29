# ENDEVA DEMO README

## Pre-requesites & Links:
- Azure Subscription 
- Azure DevOps (free version): https://dev.azure.com/hstoychev87/endeva-demo
- GitHub repository: (if readed in Azure DevOps https://github.com/hstoychev/endeva-demo )
- Website address: http://40.115.0.21/

## Our end goal:
Microservices and automation Build/Deploy process CI/CD for common web application technologies such as PHP-Apache/NginX-MySQL. Docker registry, monitoring and orchestration in Cloud native environment: Azure Container Registry, Azure Kubernetes Services, Azure DevOps.

## Build steps:

1. Create K8s cluster in Azure (AKS). We can use ARM tempaltes to automate AKS deployment and set specific settings as Disk size, OS type, node Kubernetes version and SSH  public key, set RBAC and networking (https://github.com/Azure/azure-quickstart-templates/blob/master/101-aks/azuredeploy.json).

NOTE: Be sure that AKS Service Principal account have OWNER role permissions(RBAC) over all AKS related Resource Groups in Azure Subscription!

1.1. We are dploying our AKS with Log Analytics workspace for general monitoring purposes.
- AKS Monitoring from Portal -> Monitor -> Containers/Insights. Aalerts can be configured by using custom query in Log Analytics worspace or with Azure Metric Alerts API.
1.2. Connect to AKS:
- Pre-rquisite: install azure CLI(https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- Use those commands: 
```
az login
sudo az aks install-cli   `in Windows will have to add the PATH (set PATH=%PATH%;C:\Users\hstoy\.azure-kubectl" or "$env:path += 'C:\Users\user\.azure-kubectl')`
az aks get-credentials -g myResourceGroup -n myCluster
kubectl get nodes
```
- Install Kompose (https://github.com/kubernetes/kompose/blob/master/docs/installation.md#windows). For Windows require "chocolatery"

1.2.1. Connect to AKS dashboard with required permissions: https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard
Browse dashboard: 
```
az aks browse --resource-group endeva-demo -n hristok8s
```
1.3. Create a Static IP: https://docs.microsoft.com/en-us/azure/aks/static-ip
- You may need to grant the service principal for your AKS cluster the Network Contributor role to the resource group where your Azure virtual network resources are deployed. 

1.4. Configure infrastructure in AKS cluster:
- Set Persistant Volume and Persistant Volume Claims. Add ReadWriteMany PV as Azurefile (needed for our web and app):
https://docs.microsoft.com/en-us/azure/aks/azure-files-dynamic-pv

2. Create and deploy Azure Container Registry
- From acr-deployment folder you can find Azure Resource Mananger/ ARM template for deploying ACR with Service Principal permission option, so AKS SP could be granted automatically with requried ACR Push and Pull permissions.

2.1. Grant AKS access to ACR if ACR already exist and deployed without our ARM template:
```https://docs.microsoft.com/en-us/azure/container-registry/container-registry-auth-aks?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr```

Bash script for granting AKS Service Principal access to ACR:
```
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```
3. Setup Azure DevOps (Az Dev):
- Setup Public/Private project 
- Add all required "Service connections": Azure Subscription for ACR and AKS, GitHub repo for build and release pipelines 
- Create GitHub Service connection using OAuth (otherwise cannot manage AKS cluster infrastructure)

3.1. Configure Builds-Pipelines 
- Set Continuous Integration CI: 
Two "Builds pipelines" for initial Persistant Volume and Volume Claim on K8s cluster and MySQL container. Run to deploy it on AKS.
- Set Continuous Delivery:
Two "Builds pipelines" for build and push docker images from GitHub repo to ACR. Both PHP and NGINX Continues Deployment CD pipelines.

4. Some Kubernetes CLI commands:

- Delete all
kubectl delete daemonsets,replicasets,services,deployments,pods,rc,pvc,pv,configmap,storageclasses,secrets --all

- Delete only Pods, ReplicaSets, Deployments, Services
kubectl delete daemonsets,replicasets,services,deployments,pods,rc --all

- Apply storage and php
kubectl apply -f aks.disk.yaml
kubectl apply -f php.deployment.yaml
