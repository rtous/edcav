# Launch an Ubuntu Docker container

* Instructions for installing Docker [here](docker.md).

## Create a working directory

Open a terminal and create a directory in your laptop for DRCAV. For instance, from your home directory (this should work on any platform):

    mkdir drcav

You need to write down the absolute path of this directory. On Linux and macOS you can know it with "pwd", on Windows you can type "echo %cd%". We will tell Docker to make this directory accessible from the container that we are going to launch. 

*NOTE: If you are using Docker Toolbox on Windows 10 please create the directory within your home directory (C:\Users\YOUR_USER_NAME\drcav)*

## Launch an Ubuntu container

Let's lauch now an Ubuntu container. We will launch it with two optional features:

* A Docker volume for the local directory YOUR_PATH/drcav, mapped into the container directory /drcav. This way the files within that local directory will be accessible from the container. On the one hand, during development it's useful to have the source code outside the container, this way you can edit it directly. On the other hand, a Docker volume may be necessary if the application keeps persistent data (data within the container gets lost when the conainer is removed).

* Map the ports 8080 and 8443 of the container to the same ports on the host machine.

Let's lauch now the container (replace YOUR_PATH with the local path to the drcav directory):

    docker run -it --name drcav -v YOUR_PATH/drcav:/drcav -p 8080:8080 -p 8443:8443 ubuntu bash

On Windows 10, if you are running Docker Desktop (not Docker Toolbox), use a typical Windows path, e.g.:

    docker run -it --name drcav -v C:\Users\YOUR_USER_NAME\drcav:/drcav -p 8080:8080 -p 8443:8443 ubuntu bash

On Windows 10 Home (Docker Toolbox):

    docker run -it --name drcav -v //c/Users/YOUR_USER_NAME/drcav:/drcav -p 8080:8080 -p 8443:8443  ubuntu bash

You should see something like this:

    root@813847d78b39:/#

Which means that you are now within an Ubuntu container. If you type

    root@813847d78b39:/# cd /drcav

You should see the contents of the drcav directory, which you can also access from outside Docker. Check that the shared folder works before moving on.

*NOTE: If you are running Docker Toolbox on Windows 10, it comes by default with a shared folder over C:\Users. If you did things as recommended you shouldn't need to do anything else. If not, open VirtualBox (Docker Toolbox runs over it) and go to configuration/shared folders. You should see a shared folder "c/Users". If not, create this shared folder (persistent, auto-mount) and link it to C:\Users*

## Differences between the Docker Ubuntu container and a typical Ubuntu installation

Within the container you will do some things in a different way:

    - Don't use 'sudo'. 
    - Need to install some tools such as vim, wget, default-jdk or git.

*You can find a summary of the Docker commands [here](../docker_userguide.md).*

