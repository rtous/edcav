# Doing the PTI lab assignments with Docker

It's not necessary that you do the PTI lab assignments at the PTI lab classroom, you can do them with your personal computer. However, if you don't have a Linux OS or you just don't want to mess up your Linux OS you may find convenient to work over Docker. 

## Working with Docker 

First you need to install Docker. In Linux you can check if it's already installed this way:

    docker -v

If not, you would need to install it. In Ubuntu you can do it this way:

    sudo apt-get update
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker $(whoami)

It's necessary to LOGOUT to let the usermod command have effect.

Windows and OSX installation procedures can be found [here](https://docs.docker.com/install/).

NOTE: If for any reason you want to try Docker at the PTI lab classroom you would need to fix a problem with the DNS (Docker replicates the nameservers from /etc/resolv.conf but ignores the localhost entries, the public nameservers do not work because of the firewall).  
    
    nmcli dev show | grep 'IP4.DNS'
    sudo vi /etc/docker/daemon.json
        {
            "dns": ["147.83.30.71", "8.8.8.8"]
        }
    sudo service docker restart

Once Docker is installed you can launch a clean Ubuntu container this way:

    docker run -it --name pti -v $HOME/WORKING_DIR:/my_volume -p 8080:8080 ubuntu bash

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
















