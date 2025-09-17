---
title: "Docker"
date: 2025-09-17T08:06:25+06:00
description: Docker
menu:
  sidebar:
    name: Docker
    identifier: docker
    weight: 10
author:
  name: Unique Karki
  image: /images/author/unique.jpeg
# hero: reads.jpeg
---

This is a collection of personal notes and references that I have gathered while learning Docker.

# Table of Contents
1. [Version](#version)
2. [List Containers](#list-containers)
3. [Remove Containers](#remove-containers)
4. [List Images](#list-images)
5. [Remove Images](#remove-images)
6. [Run Container](#run-container)


## Version
```sudo docker version```

## List Containers

To list the active containers </br>
```sudo docker ps```

To list all the containers including the inactive ones </br>
```sudo docker ps -a```

## Remove Containers
```sudo docker rm <container-name/id>```

## List Images
```sudo docker images```

## Remove Images
```sudo docker rmi <image-name>```

## Run Container
1. ```sudo docker run <image-name>```

    The above command does the following:
    - Pulls the image from docker registry (DockerHub or private ones), if not available locally
    - Creates a container
    - Starts the container

2. ```sudo docker run -d <image-name>```

    This runs the container in detach mode which means that the terminal is free to use for other commands (maybe starting another container)

3. ```sudo docker run -it <image-name>```

    - -i Tag</br>
    This tag keeps the STDIN (Standard input) open so that you can communicate with the container via. terminal

    - -t Tag</br>
    This tag allocates a pseudo-terminal so that it displays any terminal messages to the user