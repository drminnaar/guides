# Creating Images

In this example, I create a new image based off _alpine:latest_ that allows me to experiment with GIT.

Procedure:

* Create Dockerfile
* Base Dockerfile on alpine:linux
* Create image layer to install GIT and VIM
* Build image
* Run image

## Create custom [Alpine Linux] image with GIT setup

1. Create project folder with empty Dockerfile

   ```bash
   cd ~
   mkdir -p projects/docker/alpine-git
   cd !$
   touch Dockerfile
   ```

1. Create Dockerfile

   ```docker
   FROM alpine:latest

   LABEL author="drminnaar" \
         description="Official Alpine image with GIT and VIM installed"

   ENV WORKING_DIRECTORY=/projects

   RUN apk update && apk add git vim

   RUN mkdir $WORKING_DIRECTORY

   WORKDIR $WORKING_DIRECTORY
   ```

1. Build Dockerfile

   ```bash
   docker image build -t [docker-username]/alpine-git
   docker run --rm -it [docker-username]/alpine-git /bin/sh
   ```

1. Push Dockerfile

   ```bash
   docker login
   docker push [docker-username]/alpine-git:latest
   ```