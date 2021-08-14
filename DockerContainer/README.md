# Work in Docker Container

Thanks to [Very Academy YouTube Channel](https://www.youtube.com/watch?v=aMqs_y6dZw4&list=PLOLrQ9Pn6cazCfL7v4CdaykNoWMQymM_C&index=2&t=904s&ab_channel=VeryAcademy)

---

- Docker container are indepentend from out OS on which the docker is running.
- Dockerfile allows us to make one service while docker compose allows us to make multiple service.
- With docker compose we can describe and compose multiple services.
- Docker compose file is a YAML file.

## YAML file

Write the YAML file describing the services we need to build and build docker image from this YAML file.

Before build

```bash
himanshu in ~ : docker image ls
REPOSITORY   TAG          IMAGE ID       CREATED      SIZE
python       3.8-alpine   194019cb0bce   3 days ago   43.4MB
```

Hit the command to build image from the YAML file we have

```bash
docker-compose build
```

After build notice the **app** image.

```bash
REPOSITORY   TAG               IMAGE ID       CREATED          SIZE
app          django            3be01091eb22   49 seconds ago   156MB
python       3.8-alpine        194019cb0bce   3 days ago       43.4MB
python       3.8-slim-buster   6e66513aef39   3 days ago       114MB
```

But this just build an image and no container starts.

```bash
himanshu in ~ : docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Now we can make use of `docker-compose` to run a container from docker image. But in below we just setup a container using `django-compose` and `run` but remove it as soon it gets created using `--rm` as we need to perform some action beofre running container. Also since the env is setup in docker container we can create a new project using django-admin.

```bash
docker-compose run --rm app django-admin startproject DjangoProject
```

After hitting the command we can see there are no containers running as we closed them. But this create a new django application in the current folder.

To run the container we can hit below command and visit http://127.0.0.1:8000/

```bash
docker-compose up
```

```bash
(Learn-Docker) himanshu in Learn-Docker : docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                                       NAMES
fc3a5a73194a   app:django   "python3 DjangoProje…"   59 minutes ago   Up 58 minutes   0.0.0.0:8000->8000/tcp, :::8000->8000/tcp   django_container
```

Now to get another container we need only the `Dockerfile` and `docker-compose.yml` file. Don't forget to change the port on which we are binding the container port to some other port in `docker-componse.yml`. As our machine can bind one port to one port of any one container only. Also give a new name to container. And run these command

```bash
docker-compose run --rm app django-admin startproject DjangoProject
docker-compose up
```

Also we can enter the container running using below command.

```bash
docker exec -it fc3a5a73194a bash
```

In case we want to modify the some content in project but not allowed, then it might be created by root user in order to modify those files change the permission.

```bash
chown -R $USER:$USER /folder
```

Change some content and reload the page to modification. Also we can restart the container as

```bash
docker restart fc3a5a73194a
```

To stop a running container.

```bash
docker stop fc3a5a73194a
```
