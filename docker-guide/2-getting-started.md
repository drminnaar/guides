![Getting Started](https://user-images.githubusercontent.com/33935506/36488499-471ddebc-172c-11e8-9cb8-7972920cd391.png)

# Part 2 - Getting Started

This guide is all about getting up and running with Docker.

The guide is summarized as follows:

* [Docker Playground](#docker-playground)
* [Installation](#installation)
* [Docker Playground](#docker-playground)
* [Hello Docker](#hello-docker)
  * [Show Docker Version](#show-docker-version)
  * [Show Detailed Docker Version](#show-detailed-docker-version)
  * [Show Docker Information](#show-docker-information)
  * [Docker Alias](#docker-alias)
* [Get Help](#get-help)
* [Run First Container](#run-first-container)
* [Learning Resources](#learning-resources)
* [Summary](#summary)

---

## Docker Playground

If you desire to play with Docker without having to install it, please have a look at _['Play With Docker (PWD)'](https://labs.play-with-docker.com/)_.

![docker-playground](https://user-images.githubusercontent.com/33935506/36406761-d2834a2c-1601-11e8-8612-98971ca3f08f.png)

According to the official PWD documentation, PWD can be summarized as follows:

> PWD is a Docker playground which allows users to run Docker commands in a matter of seconds. It gives the experience of having a free Alpine Linux Virtual Machine in browser, where you can build and run Docker containers and even create clusters in Docker Swarm Mode.

Therefore, one is able to play with Docker without having to set anything up. Each session is limited to 4 hours, therefore it should be noted that anything you do in this sandbox environment will only be temporary.

---

## Installation

I am not going to detail the Docker installation process. Instead, please see the official [Docker installation documentation](https://docs.docker.com/install/) for detailed instructions on installing Docker on various platforms.

For convenience, I provide links to some of the common installation guides as follows:

* [Mac](https://docs.docker.com/docker-for-mac/install/)
* [Windows](https://docs.docker.com/docker-for-windows/install/)
* [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
* [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
* [Centos](https://docs.docker.com/install/linux/docker-ce/centos/)
* [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
* [Optional Linux Post Install Instructions](https://docs.docker.com/install/linux/linux-postinstall/)

---

## Hello Docker

After setup is complete, we can run the following commands to verify that Docker is running correctly.

### Show Docker Version

```docker
docker -v
```

Provides a short description of Docker version

![docker-v](https://user-images.githubusercontent.com/33935506/36407902-b0e9da76-160a-11e8-8ede-9d387833b827.png)

### Show Detailed Docker Version

```docker
docker version
```

 Provides full description of Docker version.

 ![docker-version](https://user-images.githubusercontent.com/33935506/36408049-85ea66aa-160b-11e8-85f0-2f7cec0fe968.png)

### Show Docker Information

```docker
docker info
```

Display system wide information relating to Docker.

![docker-info](https://user-images.githubusercontent.com/33935506/36410765-21ead5e4-161b-11e8-8e69-4b34ab048809.png)

To get all Docker information as a JSON document, type the following command:

```docker
docker info --format '{{ json . }}'
```

![docker-info-json](https://user-images.githubusercontent.com/33935506/36443933-d617ad00-1682-11e8-8488-357b5c487742.png)

As can be seen by image above, the result of running the command is a JSON document. The problem is that it's not easily readable. Therefore, to solve this issue one can use the [jq](https://stedolan.github.io/jq/) command-line JSON processor. To install _jq_, please see the official [installation documention](https://stedolan.github.io/jq/download/). Linux, Windows, and OSX operating systems are supported.

Once _jq_ is installed, issue the following command:

```docker
docker info --format '{{ json . }}' | jq
```

![docker-info-json-jq](https://user-images.githubusercontent.com/33935506/36447318-b5eacb0c-168c-11e8-883d-6d8a7cd6a1b4.png)

If one wanted to view a specific fragment of the JSON, then one would issue a command similar to the following:

```docker
docker info --format '{{ json .Plugins.Network }}' | jq
```

![docker-info-json-jq-plugins-network](https://user-images.githubusercontent.com/33935506/36447735-e9c14d74-168d-11e8-95cf-2d1480bda292.png)

### Docker Alias

Docker does not support the concept of an _'alias'_ in the same way _['Git'](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases)_ does for example. However, using a Linux based OS, one can easily create _'aliases'_ for Docker commands by setting up aliases in the `.bashrc` or `.bash_aliases` file.

#### Example

If one wanted to extract specific information (like server and registry information) relating to Docker, one could combine the _'docker info'_ command with the Linux _'grep'_ command as follows:

```docker
docker info | grep -iE 'server version|username|registry'
```

![docker-custom-info](https://user-images.githubusercontent.com/33935506/36411092-95f8659a-161c-11e8-9d66-0c2a43bec454.png)

But having to combine _docker_ and _grep_ commands can become tedious. Therefore, if you're running a Linux based operating system, or if you're running _[Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/about)_ then you could setup your own _docker_ command aliases as follows:

* Firstly, create/update `.bash-aliases` file:

  ```bash
  cd ~
  echo "alias docker-summary='docker info | grep -iE \"containers|running|paused|stopped|images|swarm|server version|username|registry\"'" >> ~/.bash_aliases
  source ~/.bash_aliases
  ```

  The above command will create a new alias called _'docker-summary'_ in the `.bash_aliases` file. The output from _'docker info'_ is piped into a _grep_ command. The _'grep -iE'_ part of the _grep_ command indicates a case insensitive search using extended regular expression.

* Secondly, open `.bashrc` file and ensure the following code is present or uncommented:

  ```bash
  if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
  fi
  ```

  If you had to make changes to your `.bashrc` file then you will need to run the following command:

  ```bash
  source ~/.bashrc
  ```

* For the last step we can run our docker alias command called _`docker-summary`_ as follows:

  ```bash
  docker-summary
  ```

  ![docker-summary](https://user-images.githubusercontent.com/33935506/36412319-5f6e54a2-1622-11e8-9bdf-4a1227918e2e.png)

### Get Help

To get a list of available Docker commands and their descriptions, use the following command:

```docker
docker --help
```

![docker-help](https://user-images.githubusercontent.com/33935506/36441941-cb36b260-167c-11e8-8c09-484d2a7a2674.png)

To get help for specific Docker commands, type the command followed by the _help_ switch as illustrated below:

```docker
docker image --help
```

![docker-help-image](https://user-images.githubusercontent.com/33935506/36442344-2742dd80-167e-11e8-8fd3-0811dc4f4157.png)

### Run First Container

Just like programming has the concept of a _'hello world'_ program, so too does Docker in the form of it's _'hello-world'_ image. To run your first Docker container, type the following command:

```docker
docker run hello-world
```

![docker-hello-world](https://user-images.githubusercontent.com/33935506/36412839-85e9a512-1624-11e8-925d-b56bb7e5f454.png)

Looking at the image above, one will notice that in order to run the container, Docker first had to retrieve the _'hello-world'_ image from the official Docker Registry. Once downloaded, a container could be started. Please take note of the steps that Docker had to take in order to display the _'hello world'_ message.

## Learning Resources

* Docker

  * [Official Docker Documentation](https://docs.docker.com/)
  * [Official Docker Training](https://training.docker.com/)

* Online Courses

  * [Docker Mastery: From a Docker Captain](https://www.udemy.com/docker-mastery/learn/v4/overview)
  * [Container Management Using Docker Learning Path](https://www.pluralsight.com/paths/docker)

* Books

  * [Docker Deep Dive](https://www.amazon.com/Docker-Deep-Dive-Nigel-Poulton-ebook/dp/B01LXWQUFF/ref=pd_sim_351_3/147-3879472-6764203?_encoding=UTF8&psc=1&refRID=04R9FM1FKDHHCC3AN4VB)
  * [Mastering Docker](https://www.amazon.com/Mastering-Docker-Second-Master-containerization-ebook/dp/B06X3X8JQ7/ref=sr_1_1?s=digital-text&ie=UTF8&qid=1519195638&sr=1-1&keywords=mastering+docker)
  * [The Docker Book: Containerization is the new virtualization](https://www.amazon.com/Docker-Book-Containerization-new-virtualization-ebook/dp/B00LRROTI4)

## Summary

In this guide we covered the essential steps required to getting started with Docker. The _['Play With Docker'](https://labs.play-with-docker.com)_ website provides a Docker sandbox environment that allows one to learn Docker without having to install or setup a single thing. If however you chose to install Docker, then there are a number of commands that one can run as a checklist to verify that the Docker installation was successful. We also covered how to use tools like _'grep'_ and _'jq'_ with Docker commands. Although slightly out of scope for a geting started guide, it was also demonstrated how to create aliases for Docker commands using `.bashrc` and `.bash_alaises` files. No getting started guide is complete without illustrating how to use the built-in help and so we covered that too. Lastly we ran a _'hello world`_ container as a final check to verify that Docker is installed correctly.