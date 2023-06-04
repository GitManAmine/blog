+++
title = "Comment installer Docker sur Ubuntu"
date = "2023-05-31T14:59:09+02:00"
author = "Amine"
cover = ""
tags = ["Docker", "Ubuntu"]
keywords = ["install", "docker", "ubuntu"]
description = "Aujourd'hui, nous allons installer docker sur Ubuntu."
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# Introduction 

Pour commencer, qu'est ce que `Docker` ?

*`Docker` est une technologie de conteneurisation qui permet d'exécuter des applications isolées dans des conteneurs.*

*Les `conteneurs` empaquettent les applications avec leurs dépendances et leurs bibliothèques, ce qui facilite leur déploiement et leur exécution. Avec `Docker`, vous pouvez exécuter différentes versions de `distributions Linux`, d'applications et de services tels que des serveurs web, des bases de données et des langages de programmation.*

*Il simplifie le déploiement des applications en incluant dans le conteneur tout ce qui est nécessaire à leur fonctionnement. `Docker` révolutionne le déploiement et la gestion des `applications` en offrant flexibilité, isolation et `utilisation' efficace des ressources.*

***

# Mise à jour système

```mermaid
sudo apt update -y && sudo apt upgrade -y
```

# Ajoutez le dépôt Docker en exécutant la commande :
```mermaid
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

# Télcharger le packet gnupg

```mermaid
sudo apt install gnupg
```

# Importez la clé GPG de Docker avec la commande :
```mermaid
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
# Ajouter le dépôt Docker au système :
```mermaid
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

# Ajouter le dépôt Docker au système :
```mermaid
sudo apt update
```
# Installez Docker en exécutant la commande suivante :
```mermaid
sudo apt install docker-ce docker-ce-cli containerd.io
```
# Check the docker version
```mermaid
docker --version
```

# Vérifier la version de docker
```mermaid
docker run hello-world
```  

Vous pouvez maintenant commencer à utiliser Docker pour exécuter et gérer des applications conteneurisées. Au revoir ! 👋