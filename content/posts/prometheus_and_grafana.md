+++
title = "Comment superviser et monitorer une application ?"
date = "2023-07-T05:35:29+02:00"
author = "Amine"
cover = ""
tags = ["Azure", "Prometheus", "Grafana", "Monitoring", "Supervision"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# Introduction 
Dans ce tuto, nous allons voir comment superviser et monitorer une application avec Prometheus et Grafana.
Prérequis : Helm et Kubectl installé sur le poste et un aks à disposition.

# Sommaire :
- Création du cluster AKS
Tout d'abord, j'ai créé un cluster AKS à l'aide de l'interface de ligne de commande Azure CLI. J'ai spécifié le nombre de nœuds, la taille des nœuds et d'autres paramètres pertinents pour répondre à mes besoins.
 ```mermaid
az aks create -g MyResourceGroup -n nameOfAks
```
- Installation de Prometheus, Grafana et Loki avec Hevlm
```mermaid
helm repo add loki https://grafana.github.io/loki/charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana
```

- Exposition les services kubernetes en type loadbalancer
```mermaid
kubeclt edit svc <nom_du_service> -n <nom_du_namespace>
```
et changer le type ClusterIP en LoadBalancer 
![screen](/static/images/svc.png)
il faut faire ceci sur les svc suivants : prometheus-server-ext, loki, grafana-ext
![get_svc](/static/images/get_svc.png)