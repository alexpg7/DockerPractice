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

