## Docker Installation

Install docker using apt


## Docker Post-installation steps for Linux

https://docs.docker.com/engine/install/linux-postinstall/


## Check your current distribution

```
uname -a
```

## 1.0 Running your first container

```
docker container run hello-world
```

## 1.1 Docker Images

Alpine is a lightweight Linux distribution.

```
docker image pull alpine
docker image ls  OR  docker images
```

## 1.2 Docker Container Run

```
docker container run alpine ls -l  OR  docker run alpine ls -l

docker container run alpine echo "hello from alpine"   OR   docker run alpine echo "hello from alpine"

```

Docker containers function at the application layer so they skip most of the steps VMs require and just run what is required for the app.

```
docker run -it alpine /bin/sh
uname -a
exit
```
```
docker ps   OR   docker container ls

docker ps -a   OR docker container ls -a
```

## 1.3 Container Isolation

```
docker run -it alpine /bin/ash
echo "hello-world" > hello.txt
ls
exit

```
```
docker run alpine ls    (hello.txt not present since this will be a new container)

docker ps -a

```

## 1.4 Start a container

```
docker container start <container ID>
```

Now use the docker container ls command again to list the running containers.
```
docker ps
```
We can send a command in to the container to run by using the exec command, as follows:

```
docker container exec <container ID> ls
``` 

## Image creation from a container

Let’s start by running an interactive shell in a ubuntu container:

```
docker container run -ti ubuntu bash
```
Install a package called figlet in this container

```
apt-get update
apt-get install -y figlet
figlet "hello docker"

exit
```

```
docker container ls -a
```

```
docker container commit CONTAINER_ID
```
```
docker image ls
```

Get the ID of the newly created image and tag it so it’s named ourfiglet:

```
docker image tag <IMAGE_ID> ourfiglet
docker image ls
```

Now we will run a container based on the newly created ourfiglet image:

```
docker container run ourfiglet figlet hello
```








