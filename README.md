# DockerPractice
A simple project to start understanding Docker (preparation for 42's Inception)

## ex00 First Dockerfile
Create the smallest possible docker file (and run it?)

```bash
# build and run
sudo docker build -t my-first-docker .
sudo docker run my-first-docker
#show containers
sudo docker ps -a

# remove images
sudo docker image rmi  my-first-docker
#remove containers
sudo docker rm <container_id>

sudo docker image rm -f  my-first-docker
# remove untagged images
sudo docker image prune

# remove unused containers
sudo docker container prune

sudo docker image rmi <image_id>
```

## ex01 Rebuilding
Create a Dockerfile, build and run it, create a second version, build and run it. notice how some layers are cached!

## ex02 Iterative environment

```bash
sudo docker run -it <image_name>

# if you dont have the bash comand in CMD of Dockerfile
sudo docker run -it <image_name> bash
#try some commands
```
## ex03 Volumes

These are used to keep data once a container is started.

```bash
# create volume
sudo docker volume create my-volume
# check created volumes
sudo docker volume ls

# mount on /data and create
sudo docker run --mount type=volume,src=my-volume,dst=/data -it ex03
echo "Hello" > /data/test.txt
exit

# remove all containers
sudo docker container prune

# mount on /data again and check for /data/test.txt
sudo docker run --mount type=volume,src=my-volume,dst=/data -it ex03
ls /data
exit
```
## ex04 Docker compose

A new way to build images, it's like a makefile. You specify the name of the app (``app``), where to get the resources (``build``) and the name of the container (``container_name``)

```yaml
services:
  app:
    build: .
    container_name: ex04
```

```bash
sudo docker compose up
sudo docker compose down

# to run terminal
sudo docker compose run app bash
```

## ex05 Multiple apps

Build multimple images from one docker-compose

```bash
sudo docker compose up

# check how are Images created
sudo docker images
sudo docker ps -a

# remove all images
sudo docker image prune -a
```

## ex06 Container networking

Learn how to access other containers built in the same docker compose

```bash
# detached compose (since now containers are infinitely running)
sudo docker compose up -d

# enter one of the apps
sudo docker exec -it app1 bash

# inside app1, get the app2 adress
getent hosts app2

# stop running containers and images
# optional
sudo docker compose stop
sudo docker compose down
sudo docker image prune -a
```

## ex07 Opening ports

Comunicate with the containers through external sources.

First, add the ports in the docker-compose file:

```yaml
    ports:
      - "8080:80"
```

This reads: host port 8080 -> container port 80

This time, the dockerfile will listen to the port with a simple python3 library:

```Dockerfile
FROM debian:12

RUN apt update && apt install -y python3

COPY index.html /index.html

CMD ["python3", "-m", "http.server", "80", "--directory", "/"]
```

```bash
# detached compose
sudo docker compose up -d

# make a http request through your port
curl localhost:8080
```

## ex08 Compose + Volume

To add volumes to our compose, we add this section to our compose file (inside services):

```yaml
    volumes:
      - my-volume:/data
```

And add a new volume section below services:

```yaml
volumes:
  my-volume:
```

```bash
# detached compose
sudo docker compose up -d

# enter the container and create data in the volume
sudo docker exec -it app bash
echo "data" > /data/data.txt
exit

# take the container down and compose+run it again
sudo docker compose down
sudo docker compose up -d
sudo docker exec -it app bash

# check inside /data
cat /data/data.txt
```

## ex09 Environment variables

Add this item in your compose file:

```yaml
    environment:
      APP_NAME: "my-docker-app"
      APP_VERSION: "1.0"
      MESSAGE: "Hello from Compose"
```

This compose creates environment variables inside the system of the container. It can be checked by entering inside and reading the ``env`` variables:

```bash
sudo docker exec -it app bash
env
```

```output
...
MESSAGE=Hello from Compose
APP_NAME=my-docker-app
APP_VERSION=1.0
...
```

## ex10 Environment with ``.env``

This time, we will put our variables inside a ``.env`` file. For example:

```bash
NAME=student
PROJECT=inception
LEVEL=beginner
```

And write the variables substitution inside the compose file:

```yaml
    environment:
      NAME: ${NAME}
      PROJECT: ${PROJECT}
      LEVEL: ${LEVEL}
```

Every time you build the container from the image, it will take your custom environment.

## ex11 A small multi-service application

Now, we will build different apps together, gathering all we learned. The ``docker-compose.yml`` should include all these items:

```yaml
services:

  app:
    build: ./app
    container_name: ${APP_NAME}
    ports:
      - "${APP_PORT}:80"
    environment:
      DB_HOST: db
      DB_PORT: ${DB_PORT}

  db:
    build: ./db
    container_name: ${DB_NAME}
    volumes:
      - db-data:/data

volumes:
  db-data:
```

There is nothing new, but we are using all the new things we learned. Using the ``curl`` command in the port we defined in ``.env`` for ``app``, we can get the ``index.html`` content.

```bash
curl localhost:8080
```

Now, we can enter the ``app`` container and try to connect to ``db`` by its name.

```bash
sudo docker exec -it inception-app bash
getent hosts db
```

Inside ``app``, we can connect to ``db`` by using the ``curl`` command also:

```bash
curl http://db:8000
```

since our ``db`` server is hosting ``/data`` (our volume), ``python`` automatically created a directory listing from an empty folder.

So, we succeeded working out our little network, which has the following structure:

```output
            Docker network
        ┌──────────────────────┐
        │                      │
        │   app ───────────► db│
        │    │                 │
        │    │                 │
        │    ▼                 │
        │  :8080               │
        └────┼─────────────────┘
            │
            ▼
          HOST
        localhost:8080
```

## ex12
