+++
title = "Comment superviser et monitorer une application ?"
date = "2023-07-06T14:59:09+02:00"
author = "Amine"
cover = ""
tags = ["Azure", "Prometheus", "Grafana", "Monitoring", "Supervision"]
keywords = ["", ""]
description = "Dans ce tuto, nous allons voir comment superviser et monitorer une application avec Prometheus et Grafana."
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++
---

# Introduction 
Dans ce tuto, nous allons voir comment superviser et monitorer une application avec Prometheus et Grafana.
Prérequis : Helm et Kubectl installé sur le poste et un aks à disposition.

# Etapes :
- ➡️ Création du cluster AKS
Tout d'abord, j'ai créé un cluster AKS à l'aide de l'interface de ligne de commande Azure CLI. J'ai spécifié le nombre de nœuds, la taille des nœuds et d'autres paramètres pertinents pour répondre à mes besoins.
 ```mermaid
az aks create -g MyResourceGroup -n nameOfAks
```
- ➡️ Installation de Prometheus, Grafana et Loki avec Hevlm
```mermaid
helm repo add loki https://grafana.github.io/loki/charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana
```

- ➡️ Exposition les services kubernetes en type loadbalancer
```mermaid
kubeclt edit svc <nom_du_service> -n <nom_du_namespace>
```
et changer le type ClusterIP en LoadBalancer 
![screen](/images/svc.png)
il faut faire ceci sur les svc suivants : prometheus-server-ext, loki, grafana-ext
![getsvc](/images/get_svc.png)
- Étape 4: Déployer une application qui génère des pods
```mermaid
kubectl create deploy random --dry-run=client --image=chentex/random-logger:latest -o yaml > random.yaml ; kubectl apply -f random.yaml
```

- ➡️ Intégration des données de Prometheus et Loki dans Grafana
![adddata](/images/data_prometheus.png)
![addip](/images/add_ip.png)

Entrer l'adreses ip extérieur de prometheus-server-ext
Faites de même pour Loki

- ➡️ Création de dashboards pour la surveillance
J'ai créé des dashboards personnalisés dans Grafana pour surveiller différentes métriques de mon cluster, telles que l'utilisation des ressources, l'état des pods, etc. J'ai également créé un dashboard spécifique pour afficher les journaux générés par mon application. 

![createdashboard](/images/create_dashboard.png)
Vous pouvez le configurer comme vous voulez

- ➡️ Création des alertes dans Grafana
![createrule](/images/rule.png)
![createrule](/images/rule2.png)

Vous pouvez faire des tests de charge pour vérifier que vos alertes fonctionnent bien.

Voici le résultat final de mon dashboard grafana : 

![createrule](/images/result.png)
