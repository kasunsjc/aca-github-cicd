# Creating a CI/CD pipeline for Azure Container Apps

Create a Container Apps

```powershell
RESOURCE_GROUP="rg-containerapps-github-actions"
LOCATION="westeurope"
CONTAINERAPPS_ENVIRONMENT="aca-environment"
CONTAINERAPPS_APP="album-backend-api"

az group create \
         --name $RESOURCE_GROUP \
         --location $LOCATION

az containerapp env create \
                --name $CONTAINERAPPS_ENVIRONMENT \
                --resource-group $RESOURCE_GROUP \
                --location $LOCATION

az containerapp create \
                --name $CONTAINERAPPS_APP \
                --resource-group $RESOURCE_GROUP \
                --environment $CONTAINERAPPS_ENVIRONMENT \
                --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
                --target-port 80 \
                --ingress 'external'

az containerapp show \
                --name $CONTAINERAPPS_APP \
                --resource-group $RESOURCE_GROUP \
                --query properties.configuration.ingress.fqdn
```

We should have these resources created.

![](images/resources.png)

Create an Azure Service Principal (SPN) to be used by Github Actions to authenticate to Azure.

```powershell
SUBSCRIPTION_ID=$(az account list --query [?isDefault].id -o tsv)

echo $SUBSCRIPTION_ID
# xxxxxxxx-3960-4e6f-a663-a06fdae23428

az ad sp create-for-rbac -n "spn-aca-github" --role Contributor --scope /subscriptions/$SUBSCRIPTION_ID --sdk-auth

