# MLOps deployment to Azure Container Apps

_Take advantage of insta-scaling for live inferencing_

Learn how to deploy an ML container with FastAPI and deploy it to [Azure Container Apps](https://docs.microsoft.com/azure/container-apps/overview?WT.mc_id=academic-0000-alfredodeza) with GitHub Actions. This repository gives you a good starting point with a Dockerfile, GitHub Actions workflow, and Python code.

## Generate a PAT

The access token will need to be added as an Action secret. [Create one](https://github.com/settings/tokens/new?description=Azure+Container+Apps+access&scopes=write:packages) with enough permissions to write to packages.

## Create an Azure Service Principal

You'll need the following:

1. An Azure subscription ID [find it here](https://portal.azure.com/#view/Microsoft_Azure_Billing/SubscriptionsBlade) or [follow this guide](https://docs.microsoft.com/en-us/azure/azure-portal/get-subscription-tenant-id)
1. A Service Principal with the following details the AppID, password, and tenant information. Create one with: `az ad sp create-for-rbac -n "REST API Service Principal"` and assign the IAM role for the subscription. Alternatively set the proper role access using the following command (use a real subscription id and replace it):

```
az ad sp create-for-rbac --name "CICD" --role contributor --scopes /subscriptions/$AZURE_SUBSCRIPTION_ID --sdk-auth
``` 


## Azure Container Apps

Make sure you have one instance already created, and then capture the name and resource group. These will be used in the workflow file.

## Change defaults 

Make sure you use 2 CPU cores and 4GB of memory per container. Otherwise you may get an error because loading HuggingFace with FastAPI requires significant memory upfront.

## Gotchas

There are a few things that might get you into a failed state when deploying:

* Not having enough RAM per container
* Not using authentication for accessing the remote registry (ghcr.io in this case). Authentication is always required
* Not using a PAT (Personal Access Token) or using a PAT that doesn't have write permissions for "packages".
* Different port than 80 in the container. By default Azure Container Apps use 80. Update to match the container.

If running into trouble, check logs in the portal or use the following with the Azure CLI:

```
az containerapp logs  show  --name $CONTAINER_APP_NAME --resource-group $RESOURCE_GROUP_NAME --follow
```

Update both variables to match your environment
