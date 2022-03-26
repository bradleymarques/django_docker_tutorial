# Django and Docker Tutorial

## What this tutorial will cover

+ A **Docker** container to serve...
+ A **Django** project with a **PostgreSQL** database
+ Using a simple **Vue.js** frontend
+ And with basic authentication using **Keycloak**

## Setting up the project and creating a git repo

Now navigate to GitHub in your browser and create a new repository called `django_docker_tutorial`.
I chose to add a pre-defined `.gitignore` and the Apache License 2.0.

Locally, clone the repo:

```sh
git clone git@github.com:{your_username}/django_docker_tutorial.git
cd django_docker_tutorial
```

I will assume you will commit and push regularly throughout the tutorial.

## Writing the Dockerfile

Create a new file named `Dockerfile` in this directory, and add the following to it:

```dockerfile
# syntax=docker/dockerfile:1
FROM python:3
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
WORKDIR /code
COPY requirements.txt /code/
RUN pip install -r requirements.txt
COPY . /code/
```

## Adding Python requirements

You will see in the Dockerfile that we copy a file named `requirements.txt`.
Create that file in the root directory of the project, and add the following to it:

```txt
Django>=3.0,<4.0
psycopg2>=2.8
```

You will see we are using `Django` (obviously). The [psycopg2 package](https://pypi.org/project/psycopg2/) is:

> "the most popular PostgreSQL database adapter for the Python programming language"

## Writing the docker-compose file

Now create a new file named `docker-compose.yml`. This file describes the services
that make up the software application. Add the following to it:

```yml
version: "3.9"

services:
  db:
    image: postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    depends_on:
      - db
```

You will see that there are 2 services, the `db` (Postgres database) and the `web` (Django) project.

## Creating the Django Project

Now create the Django project. We will name it `the_django_project` just to be generic.

```sh
sudo docker-compose run web django-admin startproject the_django_project .
```

If you are running Docker on Linux, the files django-admin created are owned by root.
This happens because the container runs as the root user.
Change the ownership of the new files:

```sh
sudo chown -R $USER:$USER .
```

You will see a new folder named `the_django_project` in the root directory and
some python files inside it that configure a default Django project.

You will also see that we have a new folder `data/db`.
Where did this come from? Well, if you look in the `docker-compose.yml` file,
you will see that the `db` services uses `./data/db` as a volume to hold the
Postgres database.
This should therefore be gitignored.
Open your `.gitignore` file and add the following at the bottom:

```yml
# Custom
data/
```

## References

1. [Quickstart: Compose and Django](https://docs.docker.com/samples/django/)
