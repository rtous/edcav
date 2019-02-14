# Doing the DRCAV lab assignments with Docker

It's not necessary that you do the DRCAV lab assignments at the lab classroom, you can do them with your personal computer. However, if you don't have a Linux OS or you just don't want to mess up your Linux OS you may find convenient to work over Docker. 

## Working with Docker 

First you need to install Docker. In Linux you can check if it's already installed this way:

    docker -v

If not, you would need to install it. In Ubuntu you can do it this way:

    sudo apt-get update
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker $(whoami)

It's necessary to LOGOUT to let the usermod command have effect.

Windows and OSX installation procedures can be found [here](https://docs.docker.com/install/).

Once Docker is installed you can launch a clean Ubuntu container this way:

    docker run -it --name pti -v $HOME/WORKING_DIR:/my_volume -p 8080:8080 -p 8443:8443 ubuntu bash

*NOTE 1: This example defines a Docker volume for the local directory $HOME/WORKING_DIR, mapped into the container directory /my_volume. This way the files within that local directory will be accessible from the container. On the one hand, during development it's useful to have the source code outside the container, this way you can edit it directly. On the other hand, a Docker volume may be necessary if the application keeps persistent data (data within the container gets lost when the conainer is removed).*

*NOTE 2: This example maps the ports 8080 and 8443 of the container to the same ports on the host machine.*

Within the container you will do some things in a different way:

    - Don't use 'sudo'. 
    - Need to install some tools such as vim, wget, default-jdk or git.

## Some useful docker commands

Exit a container shell:

    CTRL+P+Q

Reenter a conteiner shell:

    docker exec -it pti bash

List docker images in the machine:

    docker images

List running/stopped containers:

    docker ps -a

Stop a container:

    docker stop pti

Start a stopped container:

    docker start pti

Remove a container:

    docker rm pti

Remove an image:

    docker rmi animage

Copy a file from a container to the host:

    docker cp <containerId>:/file/path/within/container /host/path/target
















