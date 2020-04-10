# Level 1

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

## Image creation using a Dockerfile

Type the following content into a file named index.js. You can use vi, vim or several other Linux editors in this exercise.

```
var os = require("os");
var hostname = os.hostname();
console.log("hello from " + hostname);
```

As you can probably guess, Node.js will simply print out a “hello” message. We will Docker-ize this application by creating a Dockerfile. We will use alpine as the base OS image, add a Node.js runtime and then copy our source code in to the container. We will also specify the default command to be run upon container creation.

Create a file named Dockerfile and copy the following content into it. 

```
FROM alpine
RUN apk update && apk add nodejs
COPY . /app
WORKDIR /app
CMD ["node","index.js"]
```

Let’s build our first image out of this Dockerfile and name it hello:v0.1:

```
docker image build -t hello:v0.1 .
```

We then start a container to check that our applications runs correctly:

```
docker container run hello:v0.1
```

```
hello from <Container ID>
```

This Dockerfile does the following:

1. Specifies a base image to pull FROM - the alpine image we used in earlier labs.
2. Then it RUNs two commands (apk update and apk add) inside that container which installs the Node.js server.
3. Then we told it to COPY files from our working directory in to the container. The only file we have right now is our   index.js.
4. Next we specify the WORKDIR - the directory the container should use when it starts up
5. And finally, we gave our container a command (CMD) to run when the container starts.


## Image layers

First, check out the image you created earlier by using the history command (remember to use the docker image ls command from earlier exercises to find your image IDs):

```
docker image history <image ID>
```

Type the following in to your console window:

```
echo "console.log(\"this is v0.2\");" >> index.js

```

This will add a new line to the bottom of your index.js file from earlier so your application will output one additional line of text. Now we will build a new image using our updated code.

```
docker image build -t hello:v0.2 .
```

Notice something interesting in the build steps this time. In the output it goes through the same five steps, but notice that in some steps it says Using cache.

Sending build context to Docker daemon  86.15MB
Step 1/5 : FROM alpine
 ---> 7328f6f8b418
Step 2/5 : RUN apk update && apk add nodejs
 ---> Using cache
 ---> 2707762fca63
Step 3/5 : COPY . /app
 ---> 07b2e2127db4
Removing intermediate container 84eb9c31320d
Step 4/5 : WORKDIR /app
 ---> 6630eb76312c
Removing intermediate container ee6c9e7a5337
Step 5/5 : CMD node index.js
 ---> Running in e079fb6000a3
 ---> e536b9dadd2f
Removing intermediate container e079fb6000a3
Successfully built e536b9dadd2f
Successfully tagged hello:v0.2


Docker is intelligent enough to build the container in the most efficient way possible, as opposed to repeatedly building an image from the ground up each and every time.


## Image Inspection

```
docker image pull alpine
docker image inspect alpine
```

There is a lot of information in there:

the layers the image is composed of
the driver used to store the layers
the architecture / OS it has been created for
metadata of the image
…

Let’s get the list of layers:

```
docker image inspect --format "{{ json .RootFS.Layers }}" alpine
```

Alpine is just a small base OS image so there’s just one layer:

["sha256:60ab55d3379d47c1ba6b6225d59d10e1f52096ee9d5c816e42c635ccc57a5a2b"]

Now let’s look at our custom Hello image. You will need the image ID (use docker image ls if you need to look it up):

```
docker image inspect --format "{{ json .RootFS.Layers }}" <image ID>
```

Our Hello image is a bit more interesting (your sha256 hashes will vary):

["sha256:5bef08742407efd622d243692b79ba0055383bbce12900324f75e56f589aedb0","sha256:5ac283aaea742f843c869d28bbeaf5000c08685b5f7ba01431094a207b8a1df9","sha256:2ecb254be0603a2c76880be45a5c2b028f6208714aec770d49c9eff4cbc3cf25"]


We have three layers in our application. Recall that we had the base Alpine image (the FROM command in our Dockerfile), then we had a RUN command to install some packages, then we had a COPY command to add in our javascript code. Those are our layers! If you look closely, you can even see that both alpine and hello are using the same base layer, which we know because they have the same sha256 hash.


# Level 2



