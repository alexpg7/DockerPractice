# DockerPractice
A simple project to start understanding of Docker

## ex00
Create the smallest possible docker file (and run it?)

```bash
sudo docker build -t my-first-docker .
sudo docker run my-first-docker
sudo docker ps -a

sudo docker image rm  my-first-docker
sudo docker rm <container_id>

sudo docker image rm -f  my-first-docker
sudo docker image prune

sudo docker image rmi <image_id>
```

## ex01
Create a Dockerfile, build and run it, create a second version, build and run it. notice how some layers are cached!
