# Some useful Docker commands

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
















