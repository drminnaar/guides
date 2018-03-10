# Part 3 - Docker Container Management

In this section, I am going to explain what is meant by container management and how Docker enables it.

Docker Conainer Management can be summarized into 4 primary parts, namely:

* Pull Image
* Build Image
* Run Image
* Push Image

## Pull Image

The easiest way to start working with Docker images and containers is to download an existing image from a Docker _Registry_ like _Docker Hub_.

For example, to download the official MongoDB image, one would execute the following command:

```docker
docker pull mongo
```

![docker-pull](https://user-images.githubusercontent.com/33935506/36275093-8a473dc6-1292-11e8-9d88-e19a26e76a8c.png)

Once the image has been downloaded, it will be ready to run containers from it.

## Build Image

If one requires a custom container that has been tailored to your application requirements, then one needs to build an _Image_. Docker provides the means to build an _Image_ through the use of a _Dockerfile_ and a Docker _build_ command.

Below I have outlined what a Docker file (Dockerfile) could potentially look like.

```docker
FROM node:8.9.4-alpine
LABEL maintainer="Douglas Minnaar"
LABEL description="A simple React app that allows one to increase or decrease a counter"
EXPOSE 8080
RUN apk update && \
    apk upgrade && \
    apk add git && \
    rm -rf /var/cache/apk/* && \
    mkdir -p /usr/src/app/react-clicker
RUN git clone -q https://github.com/drminnaar/react-clicker.git /usr/src/app/react-clicker
WORKDIR /usr/src/app/react-clicker
RUN npm install --silent && npm cache clean --force
CMD ["npm", "start"]
```

A Docker file (mostly named Dockerfile) is used to define what an _Image_ should look like. The Docker file above can be explained as follows:

1. Create a new image that is based off the official Node image. Furthermore, use a specific image that runs off Alpine Linux (preferable because it's smaller) and uses Node version 8.9.4.
1. Add metadata to container using labels (maintainer, description)
1. _'EXPOSE'_ is used to publish a port that application will be available on
1. Use the Alpine APK package manager to run some updates and install Git. Git does not come standard on Alpine Linux therefore we need to install it.
1. Clone one of my sample projects from my personal github repository
1. _'WORKDIR'_ will set current directory to '/usr/src/app/react-clicker' (the name of the cloned repository).
1. Install all required node packages by running 'npm install'
1. _'CMD'_ is used to execute a command that will start application when image is used to start and run a new container.

To create the Docker _Image_ we need to run a build command from the same path of the Dockerfile. For example:

```docker
docker build -t username/react-clicker:1.0.0 .
```

The above command instructs Docker to create a new _Image_ called username/react-clicker:1.0.0 and to use the Docker file found in current path (directory). You could call the image anything you want. However, when it comes to uploading an image to Docker Hub, then one is required to use the naming convention of _'username/imagename'_. The _`:1.0.0`_ part is referred to as a tag (this is the actual _Image_ that will be stored in the _Repository_).

![dockerfile-image](https://user-images.githubusercontent.com/33935506/36275092-8a146ebe-1292-11e8-8630-80b594287100.png)

## Run Container

In order to run a container, one must have an _Image_ first. There are primarily 2 ways to obtain images. One can either build your own _Image_, or one can download pre-built images from a Docker registry. However, one an _Image_ is available on the local machine, then it's as simple as executing a Docker command to run a container off of that _Image_. For example, if I use the example from the [Build Image](#Build-Image) from above, all one would need to do is execute the following command to run that _Image_ as a container.

```docker
docker run --name react-clicker -p 8080:8080 -d username/react-clicker
```

The above command will have the affect of running the _'react-clicker'_ application in the background on local port 8080.

![docker-run](https://user-images.githubusercontent.com/33935506/36275095-8aa890ee-1292-11e8-9d94-ed6446ed735c.png)

## Push Image

Being able to build and run images is great! But what if one wants to share ones _Image_? The easiest way to share a Docker _Image_ is to use a Docker _Registry (public or private)_. Fortunately, Docker has made uploading an _Image_ to a _Registry_ really simple by providing the ```docker push``` command.

Using the _Image_ that we built and ran in the [Build Image](#Build-Image) and [Run Container](#Run-Container) sections respectively. Run the following command to upload _Image_ to _Registry_:

```docker
docker push username/react-clicker:1.0.0
```

Once the _Image_ has been successfully pushed to the _Repository_ on the _Registry, it will be available for others to download and use (provided you're using a public _Repository_)

![docker-push](https://user-images.githubusercontent.com/33935506/36275094-8a797dea-1292-11e8-8269-68fbed6cf5d5.png)