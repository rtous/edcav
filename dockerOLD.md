# Docker installation instructions

## Ubuntu

In Ubuntu you install Docker this way:

    sudo apt-get update
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker $(whoami)

It's necessary to LOGOUT to let the usermod command have effect.

## Mac

Mac installation procedure can be found [here](https://docs.docker.com/docker-for-mac/install/). 

## Windows

Installing on Windows may not be so easy. If you have Windows 10 Pro, Enterprise, or Education you can follow the instructions [here](https://docs.docker.com/docker-for-windows/install/). 

However, if you have Windows 10 Home you can only install [Docker Toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/). 

## Additional information

* You can find a summary of the Docker commands [here](docker_userguide.md).

* If you need to launch an Ubuntu container you can find help [here](docker_ubuntu.md).





