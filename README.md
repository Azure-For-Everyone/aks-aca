# Kubernetes (AKS)

Create a cluster in resource group **azureforeveru_group** and with a ACR **aksacademo** and give the cluster name **azureforeveryone**.

## Building for ACR

    az acr login --name aksacademo
    az acr list --resource-group azureforeveru_group --query "[].{acrLoginServer:loginServer}" --output table
    docker buildx build --platform linux/amd64 --push -t aksacademo.azurecr.io/aks-aca:1.0.0 .
    docker buildx build --platform linux/amd64 --push -t aksacademo.azurecr.io/aks-aca:1.2.0 .

## Deploying to AKS

    kubectl create namespace production
    kubectl apply -f aks-acr-deployment.yaml -n production

## Delete deployment

    kubectl delete namespace production
    kubectl delete -f aks-acr-deployment.yaml -n production

# Azure Container App

## Container environments (dev, staging, prod)

    az containerapp env create -n azureforeveryone-develop -g azureforeveru_group -l westeurope --logs-workspace-id e8184413-0524-4557-bef9-4aa1c56655aa
    az containerapp env create -n azureforeveryone-staging -g azureforeveru_group -l westeurope --logs-workspace-id e8184413-0524-4557-bef9-4aa1c56655aa
    az containerapp env create -n azureforeveryone-production -g azureforeveru_group -l westeurope --logs-workspace-id e8184413-0524-4557-bef9-4aa1c56655aa

## Create container app

    az containerapp create -n aks-aca-demo -g azureforeveru_group --environment azureforeveryone-develop --ingress external --target-port 80 --registry-server aksacademo.azurecr.io --registry-username myusername --registry-password xxx --query properties.configuration.ingress.fqdn 
    az containerapp update -n aks-aca-demo -g azureforeveru_group --image aksacademo.azurecr.io/aks-aca:1.0.0
    az containerapp update -n aks-aca-demo -g azureforeveru_group --image aksacademo.azurecr.io/aks-aca:1.2.0

## Create container up quickly

    az extension add --name containerapp --upgrade
    az containerapp up --name play-tetris -g azureforeveru_group --repo https://github.com/Azure-For-Everyone/TetrisContainer --registry-server aksacademo.azurecr.io --registry-username myusername --registry-password xxx

## Delete container app

    az containerapp delete -n aks-aca-demo -g azureforeveru_group
    az containerapp delete -n play-tetris -g azureforeveru_group
