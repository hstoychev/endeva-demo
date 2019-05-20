#### ENDEVA DEMO READMY

1. Create K8s cluster in Azure (AKS). We can use ARM tempaltes to automate AKS deployment and set specific settings as Disk size, OS type, node Kubernetes version and SSH  public key, set RBAC and networking (https://github.com/Azure/azure-quickstart-templates/blob/master/101-aks/azuredeploy.json).

1.1. We are dploying our AKS with Log Analytics workspace for general monitoring purposes.
1.2. Connect to AKS:
- Pre-rquisite: install azure CLI(https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- Use those commands: 

az login
sudo az aks install-cli  ## in Windows will have to add the PATH (set PATH=%PATH%;C:\Users\hstoy\.azure-kubectl" or "$env:path += 'C:\Users\user\.azure-kubectl')
az aks get-credentials -g myResourceGroup -n myCluster
kubectl get nodes

- Install Kompose (https://github.com/kubernetes/kompose/blob/master/docs/installation.md#windows). For Windows require "chocolatery"

1.2.1. Connect to AKS dashboard with required permissions: https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard