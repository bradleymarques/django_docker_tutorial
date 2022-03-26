# Django and Docker Tutorial

## What this tutorial will cover

+ Django
+ Docker
+ Vue
+ Basic authentication

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


## References

1. [Quickstart: Compose and Django](https://docs.docker.com/samples/django/)
