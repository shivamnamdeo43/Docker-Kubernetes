##Docker Installation

Install docker using apt


##Docker Post-installation steps for Linux

https://docs.docker.com/engine/install/linux-postinstall/


##Check your current distribution

uname -a


##1.0 Running your first container

docker container run hello-world


##1.1 Docker Images

Alpine is a lightweight Linux distribution.

docker image pull alpine
docker image ls  OR  docker images


##1.1 Docker Container Run



docker container run alpine ls -l  OR  docker run alpine ls -l

docker container run alpine echo "hello from alpine"   OR   docker run alpine echo "hello from alpine"


Docker containers function at the application layer so they skip most of the steps VMs require and just run what is required for the app.




