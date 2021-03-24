# Docker

In the context of DRCAV Docker can be useful to share development setups, to deploy applications or just to install stuff into your laptops without messing up your OS.

## Installing Docker 

Windows and OSX official installation procedures can be found [here](https://docs.docker.com/install/), but for Windows users we recommend following [this](./docker_wsl2.md).

In Linux you can check if it's already installed this way:

    docker -v

If not, you would need to install it. In Ubuntu you can do it this way:

    sudo apt-get update
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker $(whoami)
    newgrp docker

Check that Docker is installed and that you can run it without sudo executing:

    docker run hello-world

## Launch an Ubuntu container to work on the DRCAV lab assignments

If you want to use Docker to do the DRCAV lab assignments you should first create a local directory to be shared between the host and the container (a bind volume):   

	mkdir $HOME/drcav

Launch a clean Ubuntu container this way:
 
	docker run -it --name drcav -v $HOME/drcav:/drcav -p 8080:8080 -p 8443:8443 ubuntu bash

The command defines a Docker volume for the local directory $HOME/drcav, mapped into the container directory /drcav. This way the files within that local directory will be accessible from the container. During development it's useful to have the source code outside the container, this way you can edit it directly with your local tools. If you're working on one of the lab assignments, run the container and enter the /drcav directory (create it if necessary) from the beginning, performing all the steps from there. 

*NOTE 1: A Docker volume may be necessary if the application keeps persistent data (data within the container gets lost when the conainer is removed).*

*NOTE 2: This example also maps the ports 8080 and 8443 of the container to the same ports on the host machine.*

Within the container you will do some things in a different way:

    - Don't use 'sudo'. 
    - Need to install some tools such as vim, wget, default-jdk or git.

## Some useful docker commands

Exit a container shell:

    CTRL+P+Q

Reenter a conteiner shell:

    docker exec -it drcav bash

List docker images in the machine:

    docker images

List running/stopped containers:

    docker ps -a

Stop a container:

    docker stop drcav

Start a stopped container:

    docker start drcav

Remove a container:

    docker rm drcav

Remove an image:

    docker rmi animage

Copy a file from a container to the host:

    docker cp <containerId>:/file/path/within/container /host/path/target
















