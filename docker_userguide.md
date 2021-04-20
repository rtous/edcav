# Some useful Docker commands

List docker images in the machine:

    docker images

List running/stopped containers:

    docker ps -a

Stop a container:

    docker stop CONTAINER_NAME_OR_ID

Start a stopped container:

    docker start CONTAINER_NAME_OR_ID

Remove a container:

    docker rm CONTAINER_NAME_OR_ID

Remove an image:

    docker rmi IMAGE_ID

Enter a container shell:

    docker exec -it CONTAINER_NAME_OR_ID bash

Exit a container shell:

    CTRL+P+Q

Remove everything:

	docker system prune -a

Copy a file from a container to the host:

    docker cp <containerId>:/file/path/within/container /host/path/target
















