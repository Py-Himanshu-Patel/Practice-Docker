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

## Usefull Resource

- [Docker Hub](https://hub.docker.com/)
- [Docker Blog](https://www.docker.com/blog/)
- [Docker container commands](https://docs.docker.com/engine/reference/commandline/cli/)
- [Docker commands flags](https://docs.docker.com/engine/reference/run/)

## Useful commands

```bash
sudo docker info

# output

Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Build with BuildKit (Docker Inc., v0.6.1-docker)
  scan: Docker Scan (Docker Inc., v0.8.0)

Server:
 Containers: 2
  Running: 0
  Paused: 0
  Stopped: 2
 Images: 9
 Server Version: 20.10.8
```

## Interactive Container

This show docker binary exists and functional. Also hostname of a container is the container ID.

```bash
# enter the container
docker exec -it ubuntu /bin/bash
```

```bash
root@2e8d4c6cefa8:/# hostname
2e8d4c6cefa8
```

```bash
# to check out the IP of container
root@2e8d4c6cefa8:/# hostname -I
172.17.0.2 
```

```bash
# show running containers
docker ps

# show all containers
docker ps -a
```

Docker specify a random name for a container in case we do not specify one. To specify one we can do it as follow.

```bash
sudo docker run --name bob_the_container -it ubuntu /bin/bash
```

Starting a stopped container

```bash
sudo docker start bob_the_container     # by name
sudo docker start aa3f365f0f4e          # by id
```

Start a container an attach to it.

```bash
himanshu in ~ : docker start bob_the_container 
bob_the_container
himanshu in ~ : docker attach bob_the_container
root@e8109080d0ed:/# 
```

We can check the status of container using `docker ps`.

## Daemonized Containers

Daemonized containers don’t have the interactive session we’ve just used and are ideal for running applications and services.

```bash
sudo docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

we’ve used the docker run command with the -d flag to tell Docker to detach the container to the background. we can use the docker
logs command. The docker logs command fetches the logs of a container.

```bash
sudo docker logs daemon_dave        # to see last records
sudo docker logs daemon_dave  -f    # to see continuous log
```

you can get the last ten lines of a log by using `docker logs --tail 10 daemon_dave` . You can also follow the logs of a container without having to read the whole log file with `docker logs --tail 0 -f daemon_dave` . To make debugging a little easier, we can also add the -t flag to prefix our log entries with timestamps `sudo docker logs -ft daemon_dave`.

In addition to the container’s logs we can also inspect the processes running inside the container.

```bash
sudo docker top daemon_dave
```

To know more about stats of all docker containers.

```bash
docker stats

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O       BLOCK I/O   PIDS
0f4f4c018f7f   MyCont    0.22%     524KiB / 7.558GiB   0.01%     7.34kB / 0B   0B / 0B     2
```

Stats of some specific container.

```bash
docker stats MyCont

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O       BLOCK I/O   PIDS
0f4f4c018f7f   MyCont    0.27%     528KiB / 7.558GiB   0.01%     7.77kB / 0B   0B / 0B     2
```

There are two types of commands we can run inside a container: **background** and **interactive**. we can also run additional processes inside our containers using the `docker exec`.

Running a background task using `-d` flag and container name. Background task is `touch /etc/new_config_file`.

```bash
sudo docker exec -d daemon_dave touch /etc/new_config_file
```

Interactive task

```bash
sudo docker exec -it daemon_dave /bin/bash
```

Stopping a daemonized container

```bash
sudo docker stop daemon_dave
# OR
sudo docker stop c2c4e57c12c4
```
