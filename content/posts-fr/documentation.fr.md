+++
title = "Documentation projet final"
date = "2023-07-24T14:59:09+02:00"
author = "Amine"
lang = "fr"
cover = "/images/cover_wordpress.jpg"
tags = ["Azure", "Azure Devops", "Prometheus", "Grafana", "Monitoring", "Supervision", "Devops", "Wordpress", "Terraform", "Powershell", "Kubernetes"]
keywords = ["", ""]
description = "Documentation de mon dossier projet à soumettre au jury pour l'obtention du titre"
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++
---
# Documentation Technique : Déploiement de WordPress sur Azure Kubernetes Service (AKS) avec Terraform, Azure DevOps et supervision avec Grafana 🚀🛠️📊

## 📖 Sommaire
1. ➡️ [Introduction](#introduction)
     1. [Présentation](#11-présentation)
     2. [Objectifs](#12-objectifs-)
     3. [Architecture](#13-architecture-)
     4. [Prérequis](#14-prérequis)
 
2. ➡️ [Déploiement de l'Infrastructure avec Terraform](#2-déploiement-de-linfrastructure-avec-terraform-)
    1. [Création du script terraform](#21-création-du-script-terraform)
    1. [Création de la pipeline Azure Devops et configuration du stage Infra](#22-création-de-la-pipeline-azure-devops-et-configuration-du-stage-infra)

3. ➡️ [Déploiement de WordPress - Environnement Dev avec Ingress](#3-déploiement-de-wordpress---environnement-dev-avec-ingress)
    1. [Création du manifest YAML et des scripts pour l'ingress](#31-création-du-manifest-yaml-et-des-scripts-pour-lingress)
    2. [Configuration du stage Dev AKS](#configuration-du-stage-dev-pour-déployer-wordpress)

4. ➡️ [Déploiement de WordPress - Environnement Prod](#4-déploiement-de-wordpress---environnement-prod)
    1. [Configuration du stage pour l'environnement prod](#41-configuration-du-stage-pour-lenvironnement-prod)
    2. [Configuration du stage Prod aks](#42-configuration-du-stage-prod-aks)

5. ➡️ [Installation de Plugins sur WordPress avec un script](#5-installation-de-plugins-sur-wordpress-avec-un-script)
    1. [Script plugins](#51-script-pluginsps1)
    2. [Installation des Plugins pour WordPress](#52-configuration-du-stage-plugins)

6. ➡️ [Surveillance avec Grafana et Alerte](#6-surveillance-avec-grafana-et-alerte)

    1. [Configuration du Dashboard Grafana](#61-configuration-du-dashboard-grafana) 
    2. [Configuration des Alertes Grafana](#61-configuration-du-dashboard-grafana)
    3. [Test de charge](#63-test-de-charge)

7. ➡️ Conclusion 🏁
    1. [Résumé du Projet](#71-résumé-du-projet)
    2. [Points clés du projet](#72-points-clés-du-projet-)
    3. [Conclusion](#73-conclusion)
 

---

# Introduction

#### 1.1 Présentation

- J'ai été chargé de déployer une application WordPress sur Azure Kubernetes Service (AKS) en utilisant Azure DevOps. L'objectif est de créer une infrastructure fonctionnelle, sécurisée à faible coût et reproductible tout en automatisant le processus de déploiement, ce qui permettra de faciliter le déploiment, la gestion et les mises à jour ultérieures.

#### 1.2 Objectifs :

- Création d'une infrastructure Azure avec Terraform, comprenant un Virtual Network (VNet) avec deux subnets, un pour l'AKS et un pour la base de données, un Azure Database for MySQL flexible server à faible coût pour notre wordpress et un Storage Account.

- Automatisation du déploiement de WordPress sur l'AKS à l'aide d'Azure DevOps, en configurant les ressources Kubernetes nécessaires telles que les déploiements, les services, les secrets, etc.
Mise en place d'un Ingress Controller pour permettre l'accès externe à WordPress depuis le navigateur.

#### 1.3 Architecture : 
- Infra : 

![schéma de l'infra](/images/infraprojet.svg)

- Déploiement : 

![schéma de l'infra azure devops](/images/archi-devops.svg)

- Wordpress : 

![schéma de l'infra de l'appli](/images/archi-wordpress.svg)

#### 1.4 Prérequis 

Voici les prérequis et compétences nécessaires pour suivre et comprendre cette documentation : 
- Connaissances de base en Cloud Computing
  - Comprendre les concepts de base du Cloud Computing, y compris les services cloud, les modèles de déploiement (IaaS, PaaS, SaaS) et les avantages du cloud.

- Notions fondamentales de Kubernetes :
  - Avoir une compréhension de base de Kubernetes en tant que système d'orchestration de conteneurs. Comprendre les concepts de pods, de déploiements, de services, etc.

- Compréhension des conteneurs Docker:
  - Savoir ce qu'est Docker et comment il permet de créer, gérer et exécuter des conteneurs d'application de manière portable.

- Terraform :
  - Avoir des connaissances de base sur Terraform, un outil d'infrastructure en tant que code (IaC) qui permet de décrire l'infrastructure cloud en utilisant une syntaxe déclarative.

- Azure DevOps :
  - Comprendre les principes fondamentaux de Azure DevOps et comment il est utilisé pour automatiser la livraison de logiciels et les pipelines de déploiement.

- Git : 
  -  Avoir une connaissance de base de Git, un système de contrôle de version, et savoir comment cloner, pousser et extraire des référentiels Git.

- Helm : 
  - Comprendre les concepts de base de Helm, un gestionnaire de paquets pour Kubernetes, qui facilite le déploiement et la gestion d'applications Kubernetes.

- Prometheus et Grafana :
  - Avoir une compréhension de base de Prometheus, un système de surveillance et d'alerte open-source, et de Grafana, un outil d'analyse et de visualisation des métriques.     

- Scripting et automatisation :
  - Avoir des compétences de base en scripting, en particulier en PowerShell, car certains scripts sont utilisés pour la gestion de l'AKS et des ressources Azure.

- Réseaux et sécurité :
  -  Avoir des connaissances de base sur les réseaux et la sécurité cloud, y compris les groupes de sécurité réseau, les pare-feux et les règles de routage.

- Gestion des ressources Azure :
  - Comprendre comment créer et gérer des ressources Azure telles que les comptes de stockage, les comptes Kubernetes (AKS), les comptes de conteneur, etc.

- Déploiement d'applications Web:
  - Avoir une connaissance de base des applications Web, des bases de données et des exigences pour déployer des applications Web telles que WordPress.

---

# 2. Déploiement de l’Infrastructure avec Terraform 🌍

---
#### 2.1 Création du script Terraform
Avant de commencer, assurez-vous de posséder un compte Azure. Une fois cela fait, créez un Azure Repos Git où vous pourrez stocker tous vos précieux scripts et autres éléments nécessaires à votre pipeline.

Une fois que votre Azure Repos Git est prêt à l'emploi, préparez-vous à construire une pipeline efficace. Tout d'abord, créez un nouveau fichier que vous appellerez "main.tf". C'est dans ce fichier que tout se mettra en place.

Copiez et collez le script suivant dans votre fichier "main.tf" :

```mermaid
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "3.57.0"
    }
  }
}

provider "azurerm" {
  features {}
  tenant_id       = "" # peut être obtenue en faisant un $ az account show
  subscription_id = "" # peut être obtenue en faisant un $ az account show
}

data "azurerm_resource_group" "rg" {
  name = "PERSO_AMINE"
}

output "id" {
  value = data.azurerm_resource_group.rg.id
}

# Création du réseau virtuel (VNet)
resource "azurerm_virtual_network" "myVnet" {
  name                = "Vnet-wp"
  address_space       = ["10.0.0.0/8"]
  resource_group_name = "PERSO_AMINE"
  location            = "westeurope"
}

# Création du sous-réseau dans le VNet
resource "azurerm_subnet" "myAKSSubnet" {
  name                 = "subnetaks"
  resource_group_name  = "PERSO_AMINE"
  virtual_network_name = azurerm_virtual_network.myVnet.name
  address_prefixes     = ["10.240.0.0/16"]
}

# Création du cluster AKS (Azure Kubernetes Service)
resource "azurerm_kubernetes_cluster" "aks01" {
  name                = "aks-01"
  location            = "westeurope"
  resource_group_name = "PERSO_AMINE"
  dns_prefix          = "aks01-dns"

  default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_DS2_v2"
    vnet_subnet_id        = azurerm_subnet.myAKSSubnet.id
    
  }

  identity {
    type = "SystemAssigned"
  }

  depends_on = [azurerm_subnet.myAKSSubnet]
}


# Création du sous-réseau pour MySQL
resource "azurerm_subnet" "mysqlSubnet" {
  name                 = "subnetmysql"
  resource_group_name  = "PERSO_AMINE"
  virtual_network_name = azurerm_virtual_network.myVnet.name
  address_prefixes     = ["10.255.0.0/24"]

  delegation {
    name = "flexibleServerDelegation"
    service_delegation {
      name    = "Microsoft.DBforMySQL/flexibleServers"
      actions = ["Microsoft.Network/virtualNetworks/subnets/action"]
    }
  }  
}

# Création du sous-réseau pour le compte de stockage
resource "azurerm_subnet" "storageSubnet" {
  name                 = "storage-subnet"
  resource_group_name  = "PERSO_AMINE"
  virtual_network_name = azurerm_virtual_network.myVnet.name
  address_prefixes     = ["10.250.0.0/24"]
}

# Encodage en base64 des identifiants de la BDD
locals {
  admin_username = base64decode("encoder votre user")
  admin_password = base64decode("encoder votre password")
}

# Création du serveur MySQL flexible
resource "azurerm_mysql_flexible_server" "mysqlServer" {
  name                = "wordpress-aks01"
  location            = "westeurope"
  resource_group_name = "PERSO_AMINE"
  sku_name            = "B_Standard_B1s"
  backup_retention_days     = 3       
  version                      = "8.0.21"
  delegated_subnet_id          = azurerm_subnet.mysqlSubnet.id
  administrator_login       =  local.admin_username
  administrator_password    = local.admin_password
}

# Création de la database
resource "azurerm_mysql_flexible_database" "database" {
  name                = "wordpress-db"
  resource_group_name = data.azurerm_resource_group.rg.name
  server_name         = azurerm_mysql_flexible_server.mysqlServer.name
  charset             = "utf8mb4"
  collation           = "utf8mb4_unicode_ci"
}

# Création du compte de stockage 
resource "azurerm_storage_account" "storage" {
  name                     = "storagewpamine"
  resource_group_name      = data.azurerm_resource_group.rg.name
  location                 = data.azurerm_resource_group.rg.location
  account_tier             = "Standard"
  account_replication_type = "GRS"
}

# Création du compte du conteneur (dans le compte de stockage)
resource "azurerm_storage_container" "container" {
  name                  = "containerwp"
  storage_account_name  = azurerm_storage_account.storage.name
  container_access_type = "container"
}
```
#### 2.2 Création de la pipeline Azure Devops et configuration du stage Infra

ne fois que vous avez préparé votre code Terraform, il est temps de créer un pipeline dans Azure DevOps pour automatiser le déploiement de l'infrastructure.

- Étape 1 : Accédez à Azure DevOps et sélectionnez votre projet.

- Étape 2 : Dans votre projet, cliquez sur l'onglet "Pipelines" dans le menu supérieur, puis cliquez sur "Create Pipeline".

- Étape 3 : Choisissez votre référentiel (repository) source contenant vos fichiers Terraform, par exemple, Azure Repos Git ou GitHub, et connectez-le à votre projet.

Voici un exemple pour créer une pipeline ansi que le stage "infra" avec les tâches Terraform :

![Schéma](/images/create_pipeline.png)

![Schéma](/images/create_pipeline2.png)

Vous allez ensuite ajouter un agent et rechercher "Terraform" 

![Schéma](/images/create_pipeline3.png)

Vous allez séléctionner la commande correspodante, dans notre cas ce sera "Terraform init". Répétez cette tâche pour "Terraform Plan" ainsi que "Terraform Apply".
N"oubliez pas de séléctionner votre Azure Repos Git dans "Configuration Directory".

![Schéma](/images/create_tasks.png)

---

# 3. Déploiement de WordPress - Environnement Dev avec Ingress
#### 3.1 Création du manifest YAML et des scripts pour l’ingress
Pour déployer WordPress sur AKS, vous devrez créer un fichier YAML manifeste Kubernetes décrivant l'état souhaité de votre déploiement WordPress.

De plus, vous aurez besoin de scripts pour gérer le déploiement et la configuration de ressources telles que le contrôleur Ingress et les certificats SSL.

- Étape 1 : 
  - Créer un fichier YAML
  - Créez un nouveau fichier texte à l'aide d'un éditeur de texte (par exemple, Notepad++ ou Visual Studio Code) et collez le contenu suivant dans le fichier :

```mermaid
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-dev
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-dev
  template:
    metadata:
      labels:
        app: wordpress-dev
        environment: dev
    spec:
      containers:
      - name: wordpress-dev
        image: wordpress:latest
        resources:
          limits:
            cpu: "2"
            memory: "4Gi"
          requests:
            cpu: "1"
            memory: "2048Mi"
        env:
        - name: WORDPRESS_DB_HOST
          value: ""
        - name: WORDPRESS_DB_USER
          valueFrom: 
            secretKeyRef:
              name: bdd-secret
              key: user
        - name: WORDPRESS_DB_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: bdd-secret
              key: password
        - name: WORDPRESS_DB_NAME
          value: ""
        - name: WORDPRESS_DATABASE_PORT_NUMBER
          value: ""
        - name: WORDPRESS_CONFIG_EXTRA
          value: |
            define('DB_SSL', true);
            define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
        ports:
        - containerPort: 80
          name: http
        - containerPort: 443
          name: https
        volumeMounts:
        - name: wordpress-storage
          mountPath: /var/www/html/wp-content
      volumes:
      - name: wordpress-storage
        persistentVolumeClaim:
          claimName: wordpress-pvc

---
apiVersion: v1
kind: Service
metadata:
  name: wordpress-svc
spec:
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80
  - name: https
    protocol: TCP
    port: 443
    targetPort: 443
  selector:
    app: wordpress-dev

---
apiVersion: v1
kind: Secret
metadata:
  name: bdd-secret
type: Opaque
data:
  user: <entrer un user encoder en base64>
  password: <entrer un password encoder en base64>

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wordpress-pvc
spec:
  accessModes:
    - ReadWriteOnce²
  resources:
    requests:
      storage: 5Gi

```

Dans ce fichier YAML, vous pouvez personnaliser les paramètres de votre déploiement WordPress, tels que le nombre de répliques, les limites de ressources et les informations d'identification de la base de données.

ssurez-vous de remplacer <BASE64_ENCODED_DB_USER> et <BASE64_ENCODED_DB_PASSWORD> par votre utilisateur et votre mot de passe de base de données encodés en Base64.

- Étape 2 : Enregistrez le fichier YAML
  - Enregistrez le fichier en lui donnant un nom significatif tel que "wordpress-deployment.yaml". Assurez-vous qu'il se trouve dans votre Azure Repos Git.

- Étape 3 : Ajoutez un nouveau stage pour le déploiement avec les tasks suivantes :

![Schéma](/images/create_tasks_kube.png)

Pour déployer un contrôleur Ingress et gérer les certificats SSL, vous pouvez créer des scripts PowerShell qui interagiront avec votre cluster Kubernetes à l'aide de l'outil de ligne de commande kubectl. Voici des exemples de scripts PowerShell :

Vous allez créer un script appelé "helm-repo", c'est un script powershell qui va déployer le générateur de certificat, l'ingress et pleins d'autres choses utiles.
Vous allez y coller ceci : 
```mermaid
# Connexion au cluster
az account set --subscription "insérer la subscription de son abonnement azure"
az aks get-credentials --resource-group "insérer le nom de votre groupe de ressource" --name "insérer le nom de votre cluster"

# Ajout du repo pour l'ingress
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo add jetstack https://charts.jetstack.io
helm repo update

# Ajout des repo suivants : grafana, promtheus, loki

helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Deploiement de l'ingress, du générateur de certificat, de loki, de grafana et prometheus
helm install quickstart ingress-nginx/ingress-nginx 
helm install my-cert-manager jetstack/cert-manager --create-namespace --version v1.12.0 --set installCRDs=true
helm install loki grafana/loki-stack --create-namespace --set grafana.enabled=true
helm install prometheus prometheus-community/prometheus


# Paramètre obligatoire pour résoudre le problème du cluster lié à l'ingress
helm upgrade --set controller.service.externalTrafficPolicy=Local quickstart ingress-nginx/ingress-nginx

```
Le script powershell "ingress.ps1" : 

```mermaid
# Connexion au cluster
az account set --subscription "insérer la subscription de son abonnement azure"
az aks get-credentials --resource-group "insérer le nom de votre groupe de ressource" --name "insérer le nom de votre cluster"

# Commande qui stock dans une variable l'adresse ip de l'ingress
$service = kubectl get service quickstart-ingress-nginx-controller -o json | ConvertFrom-Json
$externalIP = $service.status.loadBalancer.ingress[0].ip
Write-Output $externalIP

$yaml = @"
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kuard
  annotations:
    #cert-manager.io/issuer: "letsencrypt-staging"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - $externalIP.sslip.io
    secretName: quickstart-example-tls
  rules:
  - host: $externalIP.sslip.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress-svc
            port:
              number: 80
"@

# Enregistrement du contenu YAML dans un fichier temporaire
$yamlFilePath = "$env:TEMP\kuard-ingress.yaml"
$yaml | Set-Content -Path $yamlFilePath -Encoding UTF8

```

Le manifest issuer-stagging.yaml est émetteur définit comment cert-manager demandera des certificats  : 
```mermaid
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    # The ACME server URL
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: fd@gmail.com
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-staging
    # Enable the HTTP-01 challenge provider
    solvers:
      - http01:
          ingress:
            ingressClassName: nginx
```

Remarque : Ces scripts supposent que vous avez déjà configuré votre cluster AKS et que vous disposez des autorisations nécessaires pour interagir avec lui à l'aide de kubectl.

L'émetteur de production, issuer-production.yaml : 
```mermaid
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    # The ACME server URL
    server: https://acme-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: fd@gmail.com
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-prod
    # Enable the HTTP-01 challenge provider
    solvers:
      - http01:
          ingress:
            ingressClassName: nginx
```
Puis pour finir le script powershell de l'ingress final :

```mermaid
# Connexion au cluster
az account set --subscription "insérer la subscription de son abonnement azure"
az aks get-credentials --resource-group "insérer le nom de votre groupe de ressource" --name "insérer le nom de votre cluster"

$service = kubectl get service quickstart-ingress-nginx-controller -o json | ConvertFrom-Json
$externalIP = $service.status.loadBalancer.ingress[0].ip
Write-Output $externalIP

$yaml = @"
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kuard
  annotations:
    cert-manager.io/issuer: "letsencrypt-staging"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - $externalIP.sslip.io
    secretName: quickstart-example-tls
  rules:
  - host: $externalIP.sslip.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress-svc
            port:
              number: 80
"@

# Enregistrement du contenu YAML dans un fichier temporaire
$yamlFilePath = "$env:TEMP\kuard-ingress.yaml"
$yaml | Set-Content -Path $yamlFilePath -Encoding UTF8

$service = kubectl get service quickstart-ingress-nginx-controller -o json | ConvertFrom-Json
$externalIP = $service.status.loadBalancer.ingress[0].ip
Write-Output $externalIP

$yamltest = @"
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: test
  annotations:
    cert-manager.io/issuer: "letsencrypt-prod"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - $externalIP.sslip.io
    secretName: quickstart-example-tls
  rules:
  - host: $externalIP.sslip.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress-svc
            port:
              number: 80
"@

# Enregistrement du contenu YAML dans un fichier temporaire
$yamltestFilePath = "$env:TEMP\test-ingress.yaml"
$yamltest | Set-Content -Path $yamltestFilePath -Encoding UTF8

# Création de l'Ingress avec le fichier YAML
kubectl apply -f $yamltestFilePath

```

#### Configuration du stage Dev pour déployer wordpress

Maintenant que vous avez préparé le fichier et les scripts YAML nécessaires, il est temps de configurer l'étape de développement de votre pipeline de déploiement AKS dans Azure DevOps

Ajoutez un nouveau stage puis cliquez dessus et ajouter des tasks

Install Kubectl :

- ![](/images/create_stage_dev.png)

Déploie le manifest YAML wordpress-dev.yaml

- ![](/images/create_stage_dev2.png)

Exécute le script helm-repo.ps1

- ![](/images/create_stage_dev3.png)

Exécute le script ingress.ps1

- ![](/images/create_stage_dev4.png)

Déploie le manifest issuer-staging.yaml

- ![](/images/create_stage_dev5.png)

Déploie le manifest issuer-prod.yaml

- ![](/images/create_stage_dev6.png)

Exécute le script ingress-edited.ps1

- ![](/images/create_stage_dev7.png)

---

# 4. Déploiement de WordPress - Environnement Prod

#### 4.1 Configuration du stage pour l’environnement prod

- Étape 1 : 
  - Créer un fichier YAML
  - Créez un nouveau fichier texte à l'aide d'un éditeur de texte (par exemple, Notepad++ ou Visual Studio Code) nommez le "wordpress-dev.yaml" et collez le contenu suivant dans le fichier :

```mermaid

apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-prod
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-prod
  template:
    metadata:
      labels:
        app: wordpress-prod
        environment: prod        
    spec:
      containers:
      - name: wordpress-prod
        image: wordpress:latest
        resources:
          limits:
            cpu: "2"
            memory: "4Gi"
          requests:
            cpu: "1"
            memory: "1024"
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-aks01.mysql.database.azure.com
        - name: WORDPRESS_DB_USER
          valueFrom: 
            secretKeyRef:
              name: bdd-secret
              key: user
        - name: WORDPRESS_DB_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: bdd-secret
              key: password
        - name: WORDPRESS_DB_NAME
          value: wordpress-db
        - name: WORDPRESS_DATABASE_PORT_NUMBER
          value: "3306"
        - name: WORDPRESS_CONFIG_EXTRA
          value: |
            define('DB_SSL', true);
            define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
        ports:
        - containerPort: 80
          name: http
        - containerPort: 443
          name: https
        volumeMounts:
        - name: wordpress-storage
          mountPath: /var/www/html/wp-content
      volumes:
      - name: wordpress-storage
        persistentVolumeClaim:
          claimName: wordpress-pvc

---
apiVersion: v1
kind: Service
metadata:
  name: wordpress-svc
spec:
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80
  - name: https
    protocol: TCP
    port: 443
    targetPort: 443
  selector:
    app: wordpress-prod

---
apiVersion: v1
kind: Secret
metadata:
  name: bdd-secret
type: Opaque
data:
  user: YW1pbmU=
  password: SG9tZWxhbmRlcjM3JSo=

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wordpress-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: wordpress-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: wordpress-prod
  minReplicas: 1
  maxReplicas: 2
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 90

```

#### 4.2 Configuration du stage Prod AKS

Ajoutez un nouveau stage puis cliquez dessus et ajouter des tasks

Install kubectl

- ![](/images/create_stage_prod.png)

Supprime le deploiement wordpress-dev

- ![](/images/create_stage_prod2.png)

Déploie le manifest wordpress-prod

- ![](/images/create_stage_prod3.png)

# 5. Installation de Plugins sur WordPress avec un script

#### 5.1 Script plugins.ps1

Voici le script : 

Avant de procéder à l'installation des plugins WordPress, nous avons besoin d'un script PowerShell pour automatiser le processus d'installation. 

Créez un nouveau script PowerShell nommé plugins.ps1 et ajoutez le contenu suivant :

```mermaid
# Connexion au cluster 
az account set --subscription "insérer la subscription de son abonnement azure"
az aks get-credentials --resource-group "insérer le nom de votre groupe de ressource"

# Commandes pour stocker dans des variables les choses suivantes : nom du conteneur de mon storage account, clé du compte storage
$key_storage = az storage account keys list --account-name storagewpamine --resource-group PERSO_AMINE --query "[0].value" --output tsv
Write-Host $key_storage

# Commandes qui stock dans des variables les choses suivantes : nom du pod, et l'adresse de l'ingress 
$podName = kubectl get po -o custom-columns=":metadata.name" --no-headers | Select-Object -Index 12
$result = kubectl get ingress -o json | ConvertFrom-Json
$address = $result.items[0].status.loadBalancer.ingress[0].ip

# Commandes qui install le thèmes et les plugins nécessaire à mon site wordpress 
$commands = @(
    "curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar",
    "chmod +x wp-cli.phar",
    "mv wp-cli.phar /usr/local/bin/wp",
    "cd ..",
    "chmod 777 -R html/",
    "cd html",
    "wp core install --url='https://$($address).sslip.io/' --title='<insérer le nom de votre site' --admin_user='<insérer le nom d'utilisateur de votre bdd>' --admin_password='<insérer le mot de passe de votre bdd>' --admin_email='<insérer l'adresse mail que vous souhaitez pour wordpress' --allow-root",
    "wp theme install zakra --activate --allow-root",
    "wp plugin install windows-azure-storage --activate --allow-root",
    "wp option update azure_storage_account_name 'storagewpamine' --allow-root",
    "wp option update azure_storage_account_primary_access_key '$key_storage' --allow-root",
    "wp option update default_azure_storage_account_container_name 'containerwp' --allow-root",
    "wp option update azure_storage_use_for_default_upload 'true' --allow-root",
    "wp plugin install elementor --activate --allow-root",
    "wp plugin install themegrill-demo-importer --activate --allow-root",
    "wp plugin install wp-booking-system --activate --allow-root",
    "wp plugin install wp-fastest-cache --activate --allow-root"
)

$commandsScript = $commands -join "; "

kubectl exec -it $podName -- bash -c $commandsScript

```

#### 5.2 Configuration du stage plugins 

Maintenant que nous avons le script PowerShell pour installer les plugins WordPress, configurons une nouvelle étape dans le pipeline Azure DevOps pour cette tâche

- ![](/images/create_stage_plugins.png)

# 6. Surveillance avec Grafana et Alerte

#### 6.1 Configuration du Dashboard Grafana 
Pour accéder à Grafana nous avons deux solution, soit faire un port-forward soit mettre le pod grafana en type LoadBalancer.
Nous allons voir la première solution mais si la deuxième vous intéresse vous pouvez vous rendre sur un autre de mes tutoriels, cliquez sur ce [lien](https://blog.aminebabouri.online/posts/prometheus_and_grafana/).

Bien, nous allons faire un port-forward (redirection de port) afin d'accéder à Grafana sans l'exposer au réseau extérieur. (vous devez être connecté à votre aks)

```mermaid
kubectl port-forward svc/loki-grafana 75:80
```

Puis taper sur votre navigateur : localhost:75

Vous allez arriver sur la page de conenxion de Grafana, le nom d'utilisateur par défaut de Grafana est admin, en revenche pour connaitre votre mot de passe vous devez taper cette commande car il sera encodé en base64 : 

```mermaid
kubectl get secrets loki-grafana -o yaml
```
Vous verrez alors le champ "admin-password: XXXXXXXXXXXXXXXXXXXXXXXX"

Copier le password, rendez vous sur un terminal bash puis exécuter cette commande :

```mermaid
echo -n "XXXXXXXXXXXXXXXXXXXXXXXX" | base64 --decode
```

Vous serez alors connecté à votre Grafana.

Suivez ceci pour ajouter prometheus en datas source à Grafana :

![adddata](/images/data_prometheus.png)

![addip](/images/add_ip.png)

Ensuite créer un Dashboard puis configurez vous comme vous le voulez 

![create_dashboard](/images/result.png)

#### 6.2 Configuration des Alertes Grafana

![createrule](/images/rule.png)
![createrule](/images/rule2.png)

#### 6.3 Test de charge

Pour tester notre site nous allons faire un test de charge, cela permettra de savoir si il faut allouer plus de ressources à notre AKS/Pod, mais cela nous permettra également de voir si nos alertes fonctionnent bien.

Nous allons alors utiliser une commande linux :

```mermaid
ab -n 1000 -c 1000 -t 300 <lien de votre site web>
```

Cette commande simule 500 utilisateurs simultanés pendant 5 minutes.

Voyons voir si nos alertes fonctionnent : 

![resultat](/images/rules_grafana.png)

![resultat_alert](/images/rules_grafana2.png)

---

#### 7.1 Résumé du projet
Le projet vise à déployer avec succès une instance de l'application WordPress sur le service Azure Kubernetes (AKS) en utilisant divers outils tels que Terraform, Azure DevOps, Grafana et Prometheus. L'utilisation d'Azure Kubernetes Service permet de bénéficier d'une orchestration évolutive de conteneurs, tandis que Terraform facilite la gestion de l'infrastructure en tant que code. L'intégration d'Azure DevOps assure une livraison continue et efficace, tandis que Grafana et Prometheus fournissent des métriques et des alertes pour surveiller les performances du cluster.

---

#### 7.2 Points clés du projet :
- Infrastructure en tant que Code (IaC) avec Terraform : Le projet met l'accent sur la gestion de l'infrastructure de manière déclarative grâce à Terraform. Les fichiers de configuration Terraform sont utilisés pour créer et configurer les ressources Azure nécessaires, telles que les comptes de stockage, le cluster AKS, etc.

- Déploiement d'une application Web sur AKS : L'objectif principal du projet est de déployer l'application Web WordPress sur le cluster AKS nouvellement créé. Cela implique de créer des images de conteneurs Docker, de configurer des déploiements Kubernetes, des services et des secrets pour héberger l'application.

- Intégration et Livraison Continue (CI/CD) : Azure DevOps est utilisé pour créer des pipelines d'intégration et de livraison continue. Cela permet d'automatiser le processus de déploiement, de tester et de mettre à jour l'application de manière cohérente et reproductible.

- Surveillance avec Prometheus et Grafana : Le projet inclut la mise en place de Prometheus pour la collecte de métriques et de Grafana pour la visualisation de ces métriques. Cela permet aux opérateurs de surveiller les performances du cluster AKS et de détecter rapidement les problèmes potentiels.

- Scalabilité et Haute Disponibilité : Grâce à l'utilisation d'Azure Kubernetes Service, le cluster peut être facilement mis à l'échelle pour répondre aux besoins croissants de l'application. De plus, la réplication et la distribution des charges garantissent une haute disponibilité de l'application.

---

#### 7.3 Conclusion 

Le déploiement de WordPress sur Azure Kubernetes Service avec Terraform, Azure DevOps, Grafana et Prometheus offre une approche robuste et évolutive pour héberger des applications Web modernes. L'automatisation fournie par Azure DevOps facilite grandement la gestion du cycle de vie de l'application, tandis que la surveillance avec Prometheus et Grafana garantit des performances optimales.

Le projet démontre l'importance de l'infrastructure en tant que code, où les ressources cloud sont gérées de manière reproductible, traçable et sans intervention manuelle. De plus, l'utilisation de Kubernetes permet de simplifier le déploiement et la gestion des conteneurs, offrant ainsi une flexibilité accrue pour les équipes de développement et d'exploitation.

En conclusion, ce projet constitue une base solide pour déployer d'autres applications sur Azure Kubernetes Service et offre des perspectives d'amélioration continue en explorant davantage les fonctionnalités avancées d'Azure et des outils de surveillance et de gestion connexes.

