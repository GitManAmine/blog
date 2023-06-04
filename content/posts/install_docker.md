+++
title = "How to install Docker on Ubuntu"
date = "2023-05-31T14:59:09+02:00"
author = "Amine"
cover = ""
tags = ["Docker", "Ubuntu"]
keywords = ["install", "docker", "ubuntu"]
description = "Today, we'll install Docker on Ubuntu !"
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# Introduction 

First, what is `Docker` ?

*`Docker` is a `containerization` technology that allows running isolated applications in containers.*

*`Containers` package applications along with their dependencies and libraries, making it easy to deploy and run them. With Docker, you can execute various versions of `Linux distributions`, applications, and services like web servers, databases, and programming languages.*

*It simplifies application deployment by including everything needed to run the application within the container. `Docker` revolutionizes `application` deployment and management, offering flexibility, isolation, and efficient resource `utilization`.*

***

# Update system

```mermaid
sudo apt update -y && sudo apt upgrade -y
```

# Add the Docker repository by running the command:
```mermaid
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

# Télcharger le packet gnupg

```mermaid
sudo apt install gnupg
```

# Import the Docker GPG key with the command:
```mermaid
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
# Add the Docker repository to the system:
```mermaid
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

# Update the package list again:
```mermaid
sudo apt update
```
# Install Docker by running the following command:
```mermaid
sudo apt install docker-ce docker-ce-cli containerd.io
```
# Check the docker version
```mermaid
docker --version
```

# Run this commmand: 
```mermaid
docker run hello-world
``` 
Congratulations! You have successfully installed Docker on Ubuntu 22.04. 

You can now start using Docker to run and manage containerized applications on your system. Goodbye! 👋
