#### ENDEVA DEMO READMY

1. Create K8s cluster in Azure (AKS). We can use ARM tempaltes to automate AKS deployment and set specific settings as Disk size, OS type, node Kubernetes version and SSH  public key, set RBAC and networking (https://github.com/Azure/azure-quickstart-templates/blob/master/101-aks/azuredeploy.json).

1.1. We are dploying our AKS with Log Analytics workspace for general monitoring purposes.
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

1.4. AKS Monitoring from Portal -> Monitor -> Containers/Insights. Aalerts can be configured by using custom query in Log Analytics worspace or with Azure Metric Alerts API.

1.5. Configure infrastructure in AKS cluster:
- Set Persistant Volume and Persistant Volume Claims. Add ReadWriteMany PV as Azurefile (needed for our web and app):
https://docs.microsoft.com/en-us/azure/aks/azure-files-dynamic-pv


3. Grant AKS access to ACR
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