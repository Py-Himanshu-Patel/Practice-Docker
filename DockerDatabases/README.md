# Configure PostgreSQL and MySQL

Previously we setup only a django application but now we try to have a database service as well.

## PostgreSQL

Reach out to official Docker Hub for official image of other third party software.
For PostgreSQL https://hub.docker.com/search?q=postgres&type=image

Dockerfile is  same as in previous examples.
Using YAML file of docker compose we first build the django application and then configure it for postgres SQL.

```dockerfile
# os we need in container
FROM python:3.8-slim-buster
# this make any python output appear on terminal
# set to any non zero value
ENV PYTHONUNBUFFERED=1
# a working dir
WORKDIR /django
# copy the requirements file
COPY requirements.txt requirements.txt
# install the required packages
RUN pip3 install -r requirements.txt
```

```yaml
version: "3.8"
services: 
  app: 
    build: .
    volumes: 
      - .:/django
    ports: 
      - 8000:8000
    image: app:django
    container_name: django_container
    command: python3 MyProject/manage.py runserver 0.0.0.0:8000
    # let current service of app depends on db service, so that 
    # can be created first
    depends_on: 
      - db
  db:
    # take postgres image from docker hub 
    image: postgres
    # map local dir for data to dir in docker container
    # so when we remove the container the data will persist on local storage
    volumes: 
      - ./data/db:/var/lib/postgresql/data
    environment: 
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    container_name: postgres_db
```

```bash
docker-compose build
# ....
# Successfully built 23b54f321fad
# Successfully tagged app:django
docker-compose run --rm app django-admin startproject MyProject
```

This will create a project in dir of `Dockerfile` and also we have a data folder with db in it. Now after this the django project has been created, now go in project and change the Database configuration in settings.py

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postresql',
        'NAME': 'postgres',
        'USER': 'postgres',
        'PASSWORD': 'postgres',
        'HOST': 'db',
        'PORT': 5432
    }
}
```

Run the command

```bash
docker-compose up
```

```bash
django_container | You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
django_container | Run 'python manage.py migrate' to apply them.
django_container | August 14, 2021 - 16:07:40
django_container | Django version 3.2.6, using settings 'MyProject.settings'
django_container | Starting development server at http://0.0.0.0:8000/
django_container | Quit the server with CONTROL-C.
```

We can see the 18 unapplied migration. But we can visit http://127.0.0.1:8000/. To do this lets first enter **django container**.

```bash
docker exec -it django_container /bin/bash
```

Go to manage.py dir and apply migrations

```shell
root@927e9d98c445:/django/MyProject# python manage.py makemigrations
```

## SQL

We have to change the base python image we use. As prev slim-buster image can't be used to configure SQL.

```dockerfile
# os we need in container
FROM python:3.8
# this make any python output appear on terminal
# set to any non zero value
ENV PYTHONUNBUFFERED=1
# a working dir
WORKDIR /django
# copy the requirements file
COPY requirements.txt requirements.txt
# install the required packages
RUN pip3 install -r requirements.txt
```

Update the `db` service in docker compose file to `mysql` image. Also update the django settings.py to connect MySQL.

```yaml
version: "3.8"
services: 
  app: 
    build: .
    volumes: 
      - .:/django
    ports: 
      - 8000:8000
    image: app:django
    container_name: django_container
    command: python3 MyProject/manage.py runserver 0.0.0.0:8000
    # let current service of app depends on db service, so that 
    # can be created first
    depends_on: 
      - db
  db:
    image: mysql:5.7
    environment: 
      MYSQL_DATABASE: 'django-app-db'
    volumes: 
      - ./data/mysql/db:/var/lib/mysql
```

Stop and remove the running container and then remove the previous image. Make sure the owner of directory is user and not root. I have to delete the previous MyProject, containers and image and then use below command. Another way is to switch to another dir with Dockerfile, docker-compose file and project code.

```bash
docker-compose build
docker-compose run --rm app django-admin startproject MyProject
```

```bash
django_container | 
django_container | You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
django_container | Run 'python manage.py migrate' to apply them.
django_container | August 14, 2021 - 19:57:24
django_container | Django version 3.2.6, using settings 'MyProject.settings'
django_container | Starting development server at http://0.0.0.0:8000/
django_container | Quit the server with CONTROL-C.
```

Same way we can enter the `django_container` and apply migrations.
