---
title: Docker References
tags: technical, docker
---

[![hackmd-github-sync-badge](https://hackmd.io/pjNNZfSfSeOp-Zhf-LnYMA/badge)](https://hackmd.io/pjNNZfSfSeOp-Zhf-LnYMA?view)


Below will be a compilation of potentially useful information regarding [docker][d-homepage] extracted from the given sources.

[d-homepage]: https://docs.docker.com/
[d-overview]: https://docs.docker.com/get-started/overview/
[d-tutorial]: https://docs.docker.com/get-started/

[d-install]: https://docs.docker.com/get-docker/
[d-archwiki]: https://wiki.archlinux.org/index.php/Docker
[d-archinstall]: https://wiki.archlinux.org/index.php/Docker#Installation
[d-compose]: https://docs.docker.com/compose/
[dc-tutorial]: https://docs.docker.com/compose/gettingstarted/
[dc-networking]: https://docs.docker.com/compose/networking/


# Docker Overview

[Docker Overview][d-overview] page giving us an idea on what `docker` is and why we want to use it.

## The Docker Platform

> Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.

The docker platform provides us with so called *containers*. These are loosely isolated environments that are light weight and contain everything needed to run an application, leading to no dependencies on utilities installed on the host.

Due to the containers, we have standardized environments for developers to work in. This eases and allows for faster delivery of applications. Getting an update to the customer will be as simple as *pushing the updated image to the production environment*.
<span style="color:aqua">Question: What does *image* exactly mean and refer to? Given the explanation provided further below, does it simply mean: we update the file (I would maybe even call it *script*) containing the creation and run instructions? I assume the actual updating of the application on the user side will then happen automatically when created or run with the newly updated script?</span>
The containers' properties and structure also entail that they can virtually run anywhere, which makes for high portability, and allow for dynamic scaling of an application.

## The Docker Architecture

> Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers.

![Docker architecture](https://docs.docker.com/engine/images/architecture.svg)

## Docker Objects

> To build your own **image**, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

> A **container** is a runneable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

One can control how isolated various components of a container should be (i.e. network, storage, etc). Containers can be provided with additional configuration options, besides those defined by the image, when creating or starting it.

# Installation

Docker can be installed on various platforms. Select your platform from this [installation page][d-install].

## Installation for Arch Linux

The arch linux community repository provides a docker package. The docker [arch wiki][d-archwiki] contains plenty of useful information. You can simply [install][d-archinstall] it as follows.

```
pacman -S docker
```

Enabling and starting the said `docker.service` can be done as follows.

```
systemctl enable docker.service
systemctl start docker.service
```

If running `docker info` gives you a *permission denied* error for the server information, you can add your user to the `docker` group in order to use it as a non-root user. This can be done as follows.  

```
usermod -a -G docker <user>
```

**Note:** You will need to log in anew with said user in order for the groups to take effect. Use the command `groups <user>` to display said user's groups (does not require a new log in).

> <span style=color:red>**WARNING:**</span> Anyone added to the `docker` group is root equivalent because they can use the `docker run --privileged` command to start containers with root privileges. For more information see [[3]](https://github.com/docker/docker/issues/9976) and [[4]](https://docs.docker.com/engine/security/security/).

Next, verify that you can run containers. The following command downloads the latest [Arch Linux image](https://wiki.archlinux.org/index.php/Docker#Arch_Linux) and uses it to run a Hello World program within a container:
```
docker run -it --rm archlinux bash -c "echo hello world"
```

# Docker Tutorial

## Docker tool walkthrough

The [getting started][d-tutorial] essentially provides us with the following video walkthrough, from DockerCon 2020, on the docker command line tool. It is a very nice introduction to the docker tool with clear explanations and hands-on examples&mdash;great to get a first look at docker. Below are a the sections of the video along with the corresponding time stamps.

{%youtube iqqDU2crIEQ %}

- [Motivations for docker](https://youtu.be/iqqDU2crIEQ?t=30) (0:30)
- [Docker's solution](https://youtu.be/iqqDU2crIEQ?t=167) (2:47)
- [Dockerfile](https://youtu.be/iqqDU2crIEQ?t=304) (5:04)
- [Create image](https://youtu.be/iqqDU2crIEQ?t=523) (8:44)
- [Run images](https://youtu.be/iqqDU2crIEQ?t=641) (10:41)
    - start and stop containers
    - connect ports into the image
    - run it in the background
- [Logs for containers in the background](https://youtu.be/iqqDU2crIEQ?t=918) (15:18)
- [Share images](https://youtu.be/iqqDU2crIEQ?t=995) (16:34)
    - push and pull to/from DockerHub
- [Docker Compose](https://youtu.be/iqqDU2crIEQ?t=1260) (21:00)
    - using multiple containers with a database
- [Recap](https://youtu.be/iqqDU2crIEQ?t=1594) (26:35)

## Containers in-depth

{%youtube 8fi7uSYlOdc %}

The container video in the tutorial makes a deep dive into containers and shows how to create one from scratch in `Go`. While only a simple container is being made here, it still gives great insight into how things are working. **NOTE:** Not watched.

## Github Docker Tutorial &ndash; dmarinova1 

The following tutorial gives a very nice and simple introduction to building a docker image and deploying it.

<https://github.com/dmarinova1/BSP-S6-K8s-Fundamentals/blob/master/Docker/Docker-tutorial.md>

## Running Python Dash Application

We will use [`dash` python library](https://dash.plotly.com/) to launch a simple web application on the local host. The goal is to run this application in a docker container and access it with our local browser. To make things simple and keep the focus on docker, we will use [E4L python code](https://minsky.uni.lu/gitlab/plotly/lu.uni.plotly.python/-/tree/master/E4L-graph/json_data) that uses `dash` in this tutorial. Feel free to use any other dash application or create quick and simple one yourself, the main focus will be on accessing the docker's local host from our machine.

The above github tutorial and the `Dockerfile` instructions found in the [docker references section](#Useful-Docker-References) should help you understand what the following docker file does.
- Essentially we take an archlinux image and install `python` and `pip`. Then we use `pip` to install the required python packages for our application.
- Then we create a user just for the sake of it (and to avoid running commands as root)
- We copy our folder containing the code and data into the container; and
- Lastly we set up the `CMD` which will be executed when we deploy our image. (In our case, when we run `docker run`).

```dockerfile
FROM archlinux:latest

# download python and pip
RUN pacman -Sy && pacman --noconfirm -S python python-pip
RUN pip install dash pandas

# create user to run commands under
RUN useradd -m e4l
USER e4l

COPY lu.uni.plotly.python-master-E4L-graph-json_data/E4L-graph/json_data/ /home/e4l/E4L-json

WORKDIR /home/e4l/E4L-json
CMD ["python", "E4Lgraph.py"]
```

We had a lot of difficulties reaching the webpage that `dash` was running on. The default host is set to `127.0.0.1` which is local to the container. There is no way to reach it from outside the container. After a lot of vainly research and attempts at trying to find a solution, it turns out we simply had to host the application on `0.0.0.0` inside the container and forward the required port. The following article very nicely explains and illustrates why we could not access the application hosted on `127.0.0.1` and why `0.0.0.0` was the simplest solution to the problem.  
<https://pythonspeed.com/articles/docker-connection-refused/>

`dash` by default serves on port `8050`. After specifying the host `0.0.0.0` in dash with the `dash.Dash.run_server` method, we can finally build and run our container as follows.

```
# docker build -t e4l-python-graph .
# docker run -p 8050:8050 e4l-python-graph
```

The last command gives the following output on my end.
```
Dash is running on http://0.0.0.0:8050/

 * Serving Flask app "E4Lgraph" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 
```

**NOTE:** To kill the application simply use `^C` as you normally would if you ran it directly on your machine. This will kill the application and then terminate and exit from the container.

We can then access the dash application in our browser through <localhost:8050>.

# Useful Docker References

The following link contains a list of available `Dockerfile` instructions along with a short sentence of what they do. 
<https://www.fosstechnix.com/dockerfile-instructions/>

# Docker-Compose

An overview of docker compose can be found in the [docker docs][d-compose].

A getting started tutorial is also available [in the docs][dc-tutorial]. In here you will among other things be exposed to the concept of *services*&mdash;although not much detail is provided.

# Docker Network

The [networking][dc-network] essentially allows us to place our containers in a network of containers. One can think of it as allowing each of the services to be part of a collection of other services. Communication between services can then occur via the container's local IP addresses within the network. One also has the possibility for services to communicate across networks through a container's IP address as exposed outside of its network.

Within the docker-compose file we distinguish between two `networks` specifications, as briefly outlined in the following.

## Top-level networks key

The `networks` specification in the top level of the YAML file allows us to specify [our own networks](https://docs.docker.com/compose/compose-file/compose-file-v2/#network-configuration-reference) to be created. If this is not specified, a default network going by the name `<app-directory-name>_default` is created, and each service joins this network.

## Service-level networks key

The `networks` specification in a service on the other hand, allows us to specify [which network a service should join](https://docs.docker.com/compose/compose-file/compose-file-v2/#networks). The networks that can be specified here correspond to the networks described and created in the top-level. As such we can easily create complex networks of services without puzzling configurations.

