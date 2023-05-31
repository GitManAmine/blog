+++
title = "Deploy Nginx on AKS with a Pipeline"
date = "2023-05-30T22:12:30+02:00"
author = "Amine"
language = "en"
cover = ""
tags = ["CI/CD", "Azure Devops", "Kubernetes"]
keywords = ["", ""]
description = "In this project, we we'll see how i deployed Nginx on Azure Kubernetes Service (AKS) using a CI/CD pipeline."
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

In this project, I deployed Nginx on Azure Kubernetes Service (AKS) using a CI/CD pipeline. I automated the deployment using Terraform scripts and used Azure DevOps to create the pipeline.


Infrastructure diagram : 

![Schéma](/images/infra.png)

# Étapes :
- Create a Git repository in Azure
- Create a Terraform script to create the AKS infrastructure
- Create a YAML manifest to deploy Nginx on the cluster
- Create the CI/CD pipeline in Azure DevOps

---

# Créer un référentiel Git dans Azure

1. ➡️ Sign in to your Azure DevOps account.

2. ➡️ Select the Azure DevOps organization where you want to create the Git repository.

3. ➡️  Navigate to the project where you want to create the Git repository or create a new project if needed.

4. ➡️ Click on the "Repos" tab in the top navigation bar to access the repositories page.

5. ➡️ Click the "New repository" button to create a new Git repository.

6. ➡️ In the opened popup window:
     1. Enter a meaningful "Repository name".
     2. Select "Git" as the "Version control" system.

7. ➡️ Click the "Create" button to create the Git repository.

8. ➡️ You will be redirected to the newly created repository page.

---

# Create a Terraform script 

``` 
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "3.57.0"
    }
  }
}

provider "azurerm" {
  features {}
  tenant_id       = "" # can be got with $ az account show
  subscription_id = "" # can be got with $ az account show
}

data "azurerm_resource_group" "rg" {
  name = ""
}

output "id" {
  value = data.azurerm_resource_group.rg.id
}

resource "azurerm_kubernetes_cluster" "aks" {
  name                = ""
  location            = data.azurerm_resource_group.rg.location
  resource_group_name = data.azurerm_resource_group.rg.name
  dns_prefix          = ""

  default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_D2_v2"
  }

  identity {
    type = "SystemAssigned"
  }
}

output "client_certificate" {
  value     = azurerm_kubernetes_cluster.aks.kube_config.0.client_certificate
  sensitive = true
}

output "kube_config" {
  value = azurerm_kubernetes_cluster.aks.kube_config_raw

  sensitive = true
}

```
> git push

# Create the YAML manifest to deploy Nginx

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

# Créer la pipeline

- Go to the "Pipelines" section.
- Click on "New pipeline".
- Proceed based on the source of your code (Azure Repos Git in my case).
- Select your repository.

```
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

steps:
- checkout: self

- script: az login

- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.8'
    addToPath: true
    
- task: KubectlInstaller@0
  displayName: Kubectl installer
  inputs: 
    kubectlVersion: latest

- script: |
    terraform init
    terraform plan -out=tfplan
  displayName: 'Execute Terraform init and plan'

- script: |
    terraform apply -auto-approve tfplan
  displayName: 'Execute Terraform apply'

- script: az aks get-credentials --resource-group <insérer nom du groupe de ressource> --name <insérer nom de l'aks>
  displayName: 'Get AKS credentials'
  
- script: kubectl apply -f nginx.yaml
  displayName: 'Deploy YAML manifest'

```
# Conclusion
To access your cluster, connect to it and run the following command to obtain the public IP address of your cluster: 
> kubecetl get svc

This concludes the tutorial. I hope this has been helpful. Goodbye! 👋