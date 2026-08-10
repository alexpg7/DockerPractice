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