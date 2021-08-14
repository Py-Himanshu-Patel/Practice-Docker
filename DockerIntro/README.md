# Dockerize a simple Django application

- Dockerfile contains the instruction needed to make a docker image.
- `Dockerfile` containes the instruction to build a docker image from the codebase.
- `.dockerignore` is file which ignores the listed file from getting picked up by docker image.
- Run the below command to make a docker image (we need to be in same dir as dockerfile). Also we can tag the image with a name.

    ```python
    docker build --tag python-django .
    ```

    Check the docker images present locally before hitting the command.

    ```bash
    himanshu in ~ : docker images
    REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
    hello-world   latest    d1165f221234   5 months ago   13.3kB
    ```

    and after hitting the command

    ```bash
    (Learn-Docker) himanshu in DockerIntro : docker images
    REPOSITORY      TAG          IMAGE ID       CREATED         SIZE
    python-django   latest       a09cfaccf698   6 seconds ago   84.9MB
    python          3.8-alpine   194019cb0bce   46 hours ago    43.4MB
    hello-world     latest       d1165f221234   5 months ago    13.3kB
    ```

- Run the docker container from the docker image. Here we run the command specified in dockerfile and server starts at port 8000 of docker container and we map docker-port:our-system-port in this command.

    ```bash
    docker run --publish 8000:8000 python-django
    ```

- Now visit `http://127.0.0.1:8000/home/` or add `0.0.0.0` to ALLOWED_HOSTS in settings.py and access `http://0.0.0.0:8000/home/`.

- Some useful commands.

    ```bash
    # get all docker images in system - pulled from docker hub or created in system
    docker image ls 
    ```

    ```bash
    # get all running cotainers 
    docker ps
    ```

    To remove an image we first need to stop and remove the container it is serving.

    ```bash
    # select the container id
    docker ps
    # stop and remove the container - let ba4e1fa71832
    docker stop ba4e1fa71832
    # remove the container 
    docker rm ba4e1fa71832
    # get the image id
    docker image ls
    # remove the image - let 3be01091eb22
    docker rmi 3be01091eb22
    ```

- If the build fails then user/owner of directory may me root change it as follow

```bash
sudo chown -R $USER <path-to-folder>
```
