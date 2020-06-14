---
layout: post
current: post
cover: assets/images/cover-img/c21.jpg
navigation: True
title: What is Docker and Container
date: 2020-06-12
comments: true
---

**This is an intoduction of container and docker technology and how to use it for your application**

---

Container is a hot topic nowadays and many people like to compare it with good old Virtual Machine, which is also an abstrction of certain things.

**What's the difference?**

**Virtual Machine** is an absctraction of physical hardware such as CPU, Disks, Rams, the whole shebang basically. In VMs, we need a hypervisor to distribute the physical hardware and abstract a portion to each VM. One thing to note here is hypervisor is not chopping the hardwares. Instead, each VM gets its portion as virtual hardware and obtains the processing power per that portion. On top of eahc VM is a dedicated OS. You might already be aware that OS will steal some hardware resources from VM before we even run any appliccations. Plus, abstrcting hardwares will bring a lot of overhead than actual needed. s

**Container** is an abstraction at the application level it relies on a docker runtime to distribute resources. That being said, all containers share the same OS, which sits on one set physical hardwares. By that, container is much more lightweight than virtual machine and is better favored thesedays. With Container, each application will run completely independent of the others.

In this post I will focus on **container** and how we can use this technology to run an application.

**Some jargons**

It's always a good practice to introduce some jargons before I start.

- **Containerized application**: the application that runs inside a container

- **Docker**: a container runtime that is needed to run your containerized application. Some other runtimes include CRI-O, Containerd, etc. However, Docker is the most popular one these days, and is owned by Docker Inc.

- **Container/Docker Images**: a "pre-packed" application that has the source codes, all dependencies and packages, which will become a container at runtime.

- **Dockerfile**: a text instructions defined by Docker. Dockerfiles describe how to assemble a private filesystem for a container, and can also contain some metadata describing how to run a container based on this image.

**Ok, Let's get started**

Basically, a workflow for creating a containerized application is as follows:

- Write the code for your applicatipn, this includes all the packages, dependencies, config files, etc.
- Put your application inside of a container by build it into a docker image
- Push the container to the registry (usually Docker Hub). You can think of Docker Hub as GitHub, if you are familiar with Git.
- Pull the container from the registry when needed.

**- Write the code for your applicatipn** <br/>
First and foremost, you have to write your code no matter if you want to use container technology or run as a normal application.

**- Build your application into a docker image** <br/>
Secondly, if you want to containerize it, here are a few steps:

1. If you haven't already, please download <a href="https://www.docker.com/products/docker-desktop" style="font-weight: normal;">Docker </a>
2. At application root level, run `docker image build -t <docker_image_name> .` Don't forget the trailing dot, which means I want to build the image out of all reources under the root directory. Each image is stored as a tag by `-t`. Remember we need a Dockerfile to actully build the image.
3. Check that your imgae has been successfully built by `docker images` and find the names of your application. Or you can find the specific image by `docker images <docker_image_name>`
4. Simple as this, you can now run your application! Try `docker run -d --name <name_of_the_running_container> -p <runing_port> <docker_image_name>`

   `-d`: we run the container in a detached mode. If you don't specify this, the container will run in foreground mode as default. Detached mode allows you to start your container in backgroudn. That means, you start yo the cotainer and could use the console after for other commnads. I alwayse use detached mode when I run my application but it's up to personal scenario.

   `--name`: if you don't specify a name, the Docker daemon will generate a randome string for you as the name.

   `-p`: bind the port you will have your application running. It's usually of the form `<local_port>:<http_port>`. For example, `<8000:8080>` where 8000 is the host's port (assume it's not in use) and 8080 is standard http port

**- Push the container to the registry** <br/>
After that, you can push your docker image to a registry. You can actually do it right after you create the image in step 2. I will use Docker Hub as my regsitry.
You will need a Docker Hub account before this. After creating an account, Click `Create Repository` and give it a name and description and click `Create`. At this time, this repo is empty. All you need to do to push your image to this repo. however, there’s one thing you must do first: images must be namespaced correctly to share on Docker Hub. Specifically, you must name images like `<your_docker_ID>/<repository_name>:<tag>` and push your image by `docker push <your_docker_ID>/<repository_name>:<tag>`

**- Pull the container from the registry** <br/>
Pulling an image from Docker Hub is as simple as `docker pull <repository_name>:<tag>`. If `<tag>` is not specified, latest version as default will be pulled down.
s
