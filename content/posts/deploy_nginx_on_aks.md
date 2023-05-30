+++
title = "Déployer Nginx sur AKS avec une pipeline"
date = "2023-05-30T22:12:30+02:00"
author = "Amine"
cover = ""
tags = ["CI/CD", "Azure Devops", "Kubernetes"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++
---
Dans ce projet, j'ai déployé Nginx sur Azure Kubernetes Service (AKS) en utilisant une pipeline CI/CD. J'ai automatisé le déploiement en utilisant des scripts Terraform et j'ai utilisé Azure DevOps pour créer la pipeline.

Schéma de l'infrastructure : 

![Schéma](/images/infra.png)

# Étapes :
- Créer un référentiel Git dans Azure
- Créer un script Terraform pour créer l'infrastructure AKS
- Créer un manifeste YAML pour déployer Nginx sur le cluster
- Créer la pipeline CI/CD sur Azure Devops

---

# Créer un référentiel Git dans Azure

1. ➡️ Connectez-vous à votre compte Azure DevOps

2. ➡️ Sélectionnez l'organisation Azure DevOps dans laquelle vous souhaitez créer le référentiel Git.

3. ➡️ Accédez au projet dans lequel vous souhaitez créer le référentiel Git, ou créez un nouveau projet si nécessaire.

4. ➡️ Cliquez sur l'onglet "Repos" dans la barre de navigation supérieure pour accéder à la page des référentiels.

5. ➡️ Cliquez sur le bouton "New repository" (Nouveau référentiel) pour créer un nouveau référentiel Git.

6. ➡️ Dans la fenêtre contextuelle qui s'ouvre :
     1. Entrez un "Repository name" (Nom du référentiel) significatif.
     2. Sélectionnez "Git" comme système de "Version control" (Contrôle de version).

7. ➡️ Cliquez sur le bouton "Create" (Créer) pour créer le référentiel Git.

8. ➡️ Vous serez redirigé vers la page du référentiel nouvellement créé.

---

# Créer un script terraform 

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
  tenant_id       = ""
  subscription_id = ""
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
# Créer la pipeline

- Se rendre dans la séction "Pipelines"
- Cliquer sur "New pipeline"
- Poursuivre en fonction de la source de notre code (Azure Repos Git dans mon cas)
- Séléctionner son dépôt

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
# Fin
Connecter vous ensuite à votre cluster, pour connaitre l'adresse ip puiblique de votre cluster, éxécutez cette commande : 
> kubecetl get svc

Au revoir 👋