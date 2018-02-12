![Getting Started](https://user-images.githubusercontent.com/33935506/36086590-fb308354-0fd5-11e8-887a-b5f7558fc34e.png)

# Part 2 - Getting Started

Before installing Docker, perhaps you would like to play with Docker in a hosted sandbox. If that's you, please have a look at [Docker Playground](https://labs.play-with-docker.com/).

## Installation

The getting started guide on Docker has detailed instructions for setting up [Docker].

After setup is complete, run the following commands to verify the success of installation:

* **docker version** - provides full description of docker version
* **docker info** - display system wide information
* **docker -v** - provides a short description of docker version
* **docker run hello-world** - pull hello-world container from registry and run it

Have a look at the [free training] offered by Docker.

Have a look at the [repository] of images offered by Docker.

### Optional Post Installation Steps For Linux Users

If you find yourself having to pre-pend every Docker command with sudo, then follow the following steps.

To create the docker group and add your user:

* Create the docker group

  ```bash
  sudo groupadd docker
  ```

* Add your user to the docker group

  ```bash
  sudo usermod -aG docker $USER
  ```

* Log out and log back in so that your group membership is re-evaluated.
* Verify that you can run docker commands without sudo.

---

## Docker Commands

### Get docker info

#### General

Command | Description
--- | ---
docker version | provides full description of docker version
docker -v | provides a short description of docker version
docker info | display system wide information
docker info --format '{{.DriverStatus}}' | display 'DriverStatus' fragment from docker information
docker info --format '{{json .DriverStatus}}' | display 'DriverStatus' fragment from docker information in JSON format

### Manage Images

Command | Description
--- | ---
docker image ls | shows all local images
docker image ls --filter 'reference=ubuntu:16.04' | show images filtered by name and tag
docker image pull [image-name] | pull specified image from registry
docker image rm [image-name] | remove image for specified _image-name_
docker image rm [image-id] | remove image for specified _image-id_

### Search Images

Command | Description
--- | ---
docker search [image-name] --filter "is-official=true" | find only official images having *[image-name]*
docker search [image-name] -- filter "stars=1000" | find only images having specified *[image-name]* and 1000 or more stars

### Manage Containers

#### Display Container Information

Command | Description
--- | ---
docker container ls | show all running containers
docker container ls -a | show all containers regardless of state
docker container ls --filter "status=exited" --filter "ancestor=ubuntu" | show all container instances of the ubuntu image that have exited
docker container inspect [container-name] | display detailed information about specified container
docker container inspect --format '{{.NetworkSettings.IPAddress}}' [container-name] | display detailed information about specified container using specified format
docker container inspect --format '{{json .NetworkSettings}}' [container-name] | display detailed information about specified container using specified format

#### Run Container

Command | Description
--- | ---
docker container run [image-name] | run container based on specified image
docker container run --rm [image-name] | run container based on specified imaged and immediately remove it once it stops
docker container run --name fuzzy-box [image-name] | assign name and run container based on specified image

#### Remove Container

Command | Description
--- | ---
docker container rm [container-name] | remove specified container
docker container rm $(docker container ls --filter "status=exited" --filter "ancestor=ubuntu" -q) | remove all containers whose id's are returned from *'$(...)'* list

### Manage Volumes

#### Display Volume Information

Command | Description
--- | ---
docker volume ls | show all volumes
docker volume ls --filter "dangling=true" | display all volumes not referenced by any containers
docker volume inspect [volume-name] | display detailed information on *[volume-name]*

#### Remove Volumes

Command | Description
--- | ---
docker volume rm [volume-name] | remove specified volume
docker volume rm $(docker volume ls --filter "dangling=true" -q) | remove all volumes having an id equal to any of the id's returned from *'$(...)'* list

---

[Docker]:https://docs.docker.com/engine/installation/
[free training]: https://training.docker.com
[repository]: https://hubs.docker.com
[Alpine Linux]: http://www.alpinelinux.org/