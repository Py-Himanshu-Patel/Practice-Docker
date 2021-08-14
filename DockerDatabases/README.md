# Configure PostgreSQL and MySQL

Previously we setup only a django application but now we try to have a database service as well.

Reach out to official Docker Hub for official image of other third party software.
For PostgreSQL https://hub.docker.com/search?q=postgres&type=image

Dockerfile is  same as in previous examples.
Using YAML file of docker compose we first build the django application and then configure it for postgres SQL.

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

```bash
root@927e9d98c445:/django/MyProject# python manage.py makemigrations
```
