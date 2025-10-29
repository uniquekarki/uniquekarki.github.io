---
title: "Docker Introduction"
date: 2025-09-17T08:06:25+06:00
description: Docker Introduction
menu:
  sidebar:
    name: Introduction
    identifier: intro
    parent: docker
    weight: 1
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
7. [Stop Container](#stop-container)
8. [Inspect Container](#inspect-container)
9. [Docker Log](#docker-log)
10. [Port Mapping](#port-mapping)
11. [Volume Mounting](#volume-mounting)
12. [Creating Images](#creating-images)
12. [CMD VS ENTRYPOINT](#cmd-vs-entrypoint)
13. [Docker Compose](#docker-compose)

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

## Stop Container

```sudo docker stop <container-name/id>```

## Inspect Container

```sudo docker inspect <container-name/id>```

Displays more detail than ps.

## Docker Log

```sudo docker log <container-name/id>```
Displays the log of the container.

## Port Mapping
1. ```sudo docker run <image-name>```
    Suppose you have a docker engine that hosts a website at 0.0.0.0:8080. When you go to that link the website doesn't open as this link is internal to the docker container. To access it using your IP and port number you have to map the port.

2. ```sudo docker run -p 80:8080 <image-name>```
    Now when you go to the link <your-IP-address>:80 the website opens

## Volume Mounting
When a docker container completes its lifecycle and stops the data contained in it will also be lost. To prevent this loss of important data from docker container we use volume mounting. This is a persistent storage mechanism that helps store data outside container in docker host.

1. Volume mounting

    ```sudo docker run -v <dir-name>:<docker-data-path> <image-name>```
    
    The above command saves the data present in <docker-data-path> to docker’s default volume location /var/lib/docker/volumes/<dir-name>.

2. Bind mounting

    ```sudo docker run -v <local-path>:<docker-data-path> <image-name>```

    The above command performs similarly to volume mounting but it saves the data in the specified location (<local-path>) in host engine.

Newer style to perform this is:
1. Volume mounting

    ```sudo docker run --mount type=volume,source=<volume-name>,target=<container-path> <image-name>```
2. Bind mounting

    ```sudo docker run --mount type=bind,source=<local-path>,target=<docker-data-path> <docker-image>```

## Creating Images

Steps to follow to run an application:
1. Start by defining base image or OS
2. Update apt repo
3. Install dependencies using apt
4. Intall program specific dependencies (eg. using pip for python)
5. Copy source code to /opt folder
6. Run the server

Following the above steps we create a Dockerfile as shown in the sample below.

Dockerfile:
```
FROM Ubuntu                 <--------------------------------- Starts from a base image or OS

RUN apt-get update</br>     <--------------------------------- Install dependencies
RUN apt-get install python                                   | 
                                                             | 
RUN pip install flask</br>                                   |
RUN pip install flask-mysql <---------------------------------

COPY . /opt/source-code     <--------------------------------- Copy source code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run <------ Specify entrypoint to run the app
```

Now to build an image:

```sudo docker build Dockerfile -t <docker-registry>/<username>/<image-name>```

To push the docker image in docker-registry:

```sudo docker push <docker-registry>/<username>/<image-name>```

## CMD VS ENTRYPOINT

Both CMD and ENTRYPOINT are instructions used to define what command should be run when a container starts.

1. CMD

    We usually go with the following command to run an instruction

    ```sudo docker run <image> <command>```
    
    Example:

    ```sudo docker run ubuntu sleep 10```
    This runs the docker container using the image and sleeps after 10 seconds. But the use of sleep looks revealing.

    To implement thi using CMD we modify the Dockerfile like:
    
    Dockerfile

    ```
    FROM Ubuntu
    .
    .
    .

    CMD sleep 10 / CMD ["sleep","10"]
    ```

2. ENTRYPOINT

    Suppose we want to specify certain parameters during the run time of the command. This is not allowed by CMD as we have to explicitly define the parameters (like 10 for sleep). ENTRYPOINT allows to set such parameters.

    Dockerfile

    ```
    FROM Ubuntu
    .
    .
    .
    ENTRYPOINT ["sleep"]
    ```

    Example:

    ```sudo docker run ubuntu 10```
    
    This attaches 10 after the sleep command and the system ends after 10 seconds.

3. Both

    Suppose you want to use ENTRYPOINT but you dont give any arguments. This causes error during the run time. So you have to set a default parameter value when it is not set. For that you make use of both CMD and ENTRYPOINT.

    Dockerfile

    ```
    FROM Ubuntu
    .
    .
    .

    ENTRYPOINT ['sleep']
    CMD ['10']
    ```

    ```sudo docker run ubuntu 5```
    
    This makes and run the container for 5 seconds then closes it.

    ```sudo docker run ubuntu```

    This makes and run the container for default value (10 seconds) then closes it.

## Docker Compose

The problem with ```docker run```:

Suppose you make a voting application which is composed of many other application such as:
1. Voting application - Where the user casts their vote
2. In-memory database - Redis server to temporarily store the data from voting app
3. Worker application - Backend application to run all the functionalities
4. Postgres database - Where all of the voting data is stored
5. Result application - Where the voting result is displayed

In order to run all of these application at once and communicate with each other we must specify their respective links like shown below.

```
sudo docker run -d --name=redis redis

sudo docker run -d --name=db postgres:9.4

sudo docker run -d --name=vote -p 5000:80 --link= redis:redis voting-app

sudo docker run -d --name=result -p 5001:80 --link db:db result-app

sudo docker run -d --name=worker --link db:db --link redis:redis worker
```

This way the command looks tedious and unmanagable to enter one by one in Terminal. To solve this we use a .yaml file to specify all of the above.

1. Version 1

docker-compose.yaml

```
redis:                                                        <---------------- Name of container
  image: redis                                                <---------------- Name of image if already created

db:
  image: postgres:9.4

vote:
  image: voting-app
  ports:                                                      <---------------- Port mapping for voting-app
    -5000:80
  links:                                                      <---------------- Used to specify dependencies as voting-app depends upon redis application
    -redis

result:
  image: result-app  /  build: <path-to-code-with-dockerfile> <--------------- Use 'image' if image is already made else use 'build'
  ports:
    -5001:80
  links:
    -db

worker:
  image: worker
  links:
    -redis
    -db
```

2. Version 2


docker-compose.yaml

```
version 2

services:
  redis:
    image: redis

  db:
    image: postgres:9.4

  vote:
    image: voting-app
    ports:
      -5000:80
    depends_on:
      -redis

  result:
    image: result-app  /  build: <path-to-code-with-dockerfile>
    ports:
      -5001:80
    depends_on:
      -db

  worker:
    image: worker
    depends_on:
      -redis
      -db
```

3. Suppose you want to connect specific services to a dedicated network to separate the traffic. The convention for such scenario is as given below.


docker-compose.yaml

```
version 2

services:
  redis:
    image: redis

    networks:
      - back-end                <--------------- Connects redis container to 'back-end' network

  db:
    image: postgres:9.4

    networks:
      - back-end
  
  vote:
    image: voting-app
    ports:
      - 5000:80
    
    depends_on:
      - redis
    
    networks:
      - front-end               <--------------- Connects vote container to both 'back-end' and 'front-end' networks
      - back-end

network:                        <--------------- Defines the networks 
  front-end:
  back-end:
```