# MongoDB - Getting Started

## Summary

This guide covers the essentials of MongoDb in terms of getting up and running with MongoDB. The guide provides a brief introduction to MongoDb, followed by sections on tools, hosting methods and basic queries and commands.

- [What Is MongoDB](#what-is-mongodb)
- [MongoDB Setup](#mongodb-setup)
  - [Local Install](#local-install)
  - [WSL2 Install](#wsl2-install)
  - [Host Using Docker](#host-using-docker)
  - [Host Using MongoDB Atlas](#host-using-mongodb-atlas)
- [MongoDB Clients](#mongodb-clients)
  - [MongoDB Shell](#mongodb-shell)
  - [MongoDb Compass](#mongodb-compass)
  - [NoSqlBooster](#nosqlbooster)
  - [Robo 3T](#robo-3t)
  - [Visual Studio Code](#visual-studio-code)
- [Basic MongoDB Commands](#basic-mongodb-commands)
- [Conclusion](#conclusion)

---

## What Is MongoDB?

MongoDb is an open source document-oriented database that has been designed for scalability and simplicity for both developers and sysadmins. Traditional relational database management systems (RDBMS) like MSSQL, Oracle, MySQL, and PostGreSQL store data in tables having a static schema composed of rows and columns. However, MongoDB stores it's data in JSON-like documents having dynamic schemas.

For more information, see the following links:

- [Official MongoDb Website](https://www.mongodb.com/)
- [What Is MongoDb](https://www.mongodb.com/what-is-mongodb)
- [MongoDb Docs](https://docs.mongodb.com/manual/)
- [MongoDb Guides](https://docs.mongodb.com/guides/)
- [Who Uses MongoDb](https://www.mongodb.com/who-uses-mongodb)

---

## MongoDB Setup

There are a number of software offerings from MongoDb.

For more information in terms of what MongoDb offerings are available for you to try, please visit [the official MongoDb downloads website](https://www.mongodb.com/try/download)

I will discuss 4 options to get up and running with MongoDB:

- Local Install
- WSL2 Install
- Host in Docker
- MongoDB Atlas (Database As A Service)

### Local Install

MongoDb offers a community version that one can find [here](https://www.mongodb.com/try/download/community).

For specific platform/OS installation instructions, please see the following links:

- [Install MongoDB On Linux](https://docs.mongodb.com/manual/administration/install-on-linux/)
- [Install MongoDB On Windows](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)
- [Install MongoDB On OSX](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

### WSL2 Install

According to the official [MongoDb documentation](https://docs.mongodb.com/manual/administration/install-on-linux/), MongoDB does not support the _[Windows Subsystem for Linux (WSL)]_

![mongodb-wsl-support](https://user-images.githubusercontent.com/33935506/95008612-e226ca80-0677-11eb-971a-1ffd04f66357.png)

However, it is possible to install MongoDb on _[Windows Subsystem for Linux 2 (WSL2)]_

#### Instructions

- Install WSL2 as per the [official Microsoft WSL installation guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
- When choosing a Linux distribution, select an Ubuntu Linux distribution
- Install MongoDB as per the [official MongoDB installation guide](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/). A summary of the install instructions is provided below:
  
  ```bash
  # Reload local package database
  sudo apt update

  # Upgrade distro
  sudo apt full-upgrade -y

  # Import the public key used by the package management system
  wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -  

  # Create a list file for MongoDB
  # This example uses Ubuntu 18.04
  echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

  # Reload local package database
  sudo apt update

  # Install the MongoDB packages (shell, tools, server, etc)
  sudo apt-get install -y mongodb-org

  # Alternatively, one can install only the shell and tools
  sudo apt-get install -y mongodb-org-shell
  sudo apt-get install -y mongodb-org-tools

  # or only install the server
  sudo apt-get install -y mongodb-org-server
  ```

- Start MongoDB service (`mongod`)
  
  > **NOTE:** Typically the command `sudo systemctl status|start|stop|restart mongodb` is used to manage the MongoDB service. However, in order to keep WSL lightweight, `SysVinit` is used over `systemd`. Therefore, upon installing MongoDB using the commands above, an error is received that prevents MongoDB from being setup as a service. This is because systemctl requires systemd. Despite this constraint, one can still manage a MongoDB instance in WSL2 using the commands below.

  ```bash
  # Verify that the MongoDB shell has been installed
  mongo --version

  # Verify that the MongoDB server hs been installed
  mongod --version
  cat /etc/mongod.conf

  # Start a MongoDB instance
  mkdir -p ~/mongodb/data/db
  mongod --dbpath ~/mongodb/data/db --fork --logpath /dev/null

  # Connect to MongoDB instance
  mongo --host localhost --port 27017
  ```

### Host Using Docker

If you have never used [Docker] before, then this option is less suitable for you as it implies having some Docker knowledge and experience. However, if you are still interested in pursuing this option, then I recommend viewing my [Docker Guide] as a starting point. I also have a [Docker Cheatsheet] that you may find useful.

The Docker image that will be used, is the [official Docker MongoDB image](https://hub.docker.com/_/mongo).

I have created a project called [_mongodb-stack_](https://github.com/drminnaar/mongodb-stack) that provides examples of how to host _MongoDB_ in development using different techniques.

#### Example 1

Host a single node MongoDb instance using _docker_.

- Run

  ```bash
  # Create docker network that will be used by MongoDb container
  docker network create mongo-net

  # Run MongoDb container
  docker container run \
      --detach \
      --name mongo-1 \
      --publish 27017:27017 \
      --restart unless-stopped \
      --network mongo-net \
      mongo:4.4-bionic
  ```

- Connect

  ```bash
  mongo --host localhost --port 27017

  # use events_db and show events
  show dbs
  db.stats()
  ```

- Cleanup

  ```bash
  # Remove MongoDb container
  docker container rm mongo-1 --force

  # Remove docker network
  docker network rm mongo-net
  ```

#### Example 2

Host a single node MongoDb instance using _docker-compose_.

- Define stack

  ```yml
  version: "3.7"
  services:
    mongo-1:
      image: mongo:4.4-bionic
      container_name: mongo-1
      restart: unless-stopped
      ports:
        - 27017:27017
      networks:
        - default
  networks:
    default:
      name: mongo-net
      driver: bridge
  ```

- Run stack

  ```bash
  # Run MongoDb stack as a daemon
  docker-compose up -d
  ```

- Connect

  ```bash
  mongo --host localhost --port 27017

  # use events_db and show events
  show dbs
  db.stats()
  ```

- Delete stack

  ```bash
  # Remove MongoDb stack including the removal of volumes
  docker-compose down -v
  ```

#### Example 3

Host a single node MongoDb instance using _docker_. Configure a bind mount for MongoDb database files.

- Run

  ```bash
  # Create a directory to hold MongoDb data
  mkdir -p ~/mongo-stack/data/db

  # Create docker network that will be used by MongoDb container
  docker network create mongo-net

  # Run MongoDb container specifying bind mount
  docker container run \
      --detach \
      --name mongo-1 \
      --publish 27017:27017 \
      --restart unless-stopped \
      --network mongo-net \
      --volume ~/mongo-stack/data/db:/data/db \
      mongo:4.4-bionic
  ```

- Connect

  ```bash
  mongo --host localhost --port 27017

  # use events_db and show events
  show dbs
  db.stats()
  ```

- Cleanup

  ```bash
  # Remove MongoDb container
  docker container rm mongo-1 --force

  # Remove docker network
  docker network rm mongo-net

  # Remove database files (may need sudo)
  rm -r ~/mongo-stack/data/db/*
  ```

#### Example 4

Host a single node MongoDb instance using _docker-compose_. Configure a bind mount for MongoDb database files.

- Define stack

  ```bash
  # Create a directory to hold MongoDb data
  mkdir -p ~/mongo-stack/data/db
  ```

  ```yml
  version: "3.7"
  services:
    mongo-1:
      image: mongo:4.4-bionic
      container_name: mongo-1
      restart: unless-stopped
      ports:
        - 27017:27017
      networks:
        - default
      volumes:
        - type: bind
          source: ~/mongo-stack/data/db
          target: /data/db
  networks:
    default:
      name: mongo-net
      driver: bridge
  ```

- Run stack

  ```bash
  # Run MongoDb stack as a daemon
  docker-compose up -d
  ```

- Connect

  ```bash
  mongo --host localhost --port 27017

  # use events_db and show events
  show dbs
  db.stats()
  ```

- Delete stack

  ```bash
  # Remove MongoDb stack including the removal of volumes
  docker-compose down -v

  # Remove database files (may need sudo)
  rm -r ~/mongo-stack/data/db/*
  ```

#### Example 5

Host a single node MongoDb instance using _docker_. Use environment variables to specify authentication information and initial database. Specify a javascript file that will be executed as part of setup and initialization (uses entry point scripts)

- Prepare javascript file

  ```bash
  # Create scripts folder
  mkdir -p ~/mongo-stack/scripts

  # Create events.js in scripts folder
  cat << EOF > ~/mongo-stack/scripts/events.js
  db.events.insertOne({
        "_id": "5f66c8ddba2c5ed57bd10c62",
        "name": "mollit",
        "isActive": true,
        "address": "300 Independence Avenue, Bedias, Virgin Islands, 3014",
        "about": "Et officia mollit fugiat ut amet. Cupidatat officia commodo est sit nostrud ea quis tempor. Tempor ex proident minim tempor labore proident laborum id excepteur.",
        "date": "Friday, August 30, 2019 6:09 PM",
        "latitude": "67.018372",
        "longitude": "138.780409",
        "tags": [
            "eiusmod",
            "officia",
            "deserunt",
            "nostrud",
            "nulla"
        ]
    });
  EOF
  ```

- Run

  ```bash
  # Create docker volume
  docker volume create mongo-data-db

  # Create docker network that will be used by MongoDB container
  docker network create mongo-net

  # Run MongoDB container specifying environment variables
  # Take note of the 'docker-entrypoint-initdb.d' bind mount
  docker container run \
      --detach \
      --name mongo-1 \
      --publish 27017:27017 \
      --restart unless-stopped \
      --network mongo-net \
      --volume mongo-data-db:/data/db \
      --volume ~/mongo-stack/scripts/events.js:/docker-entrypoint-initdb.d/events.js:ro \
      --env MONGO_INITDB_ROOT_USERNAME=root \
      --env MONGO_INITDB_ROOT_PASSWORD=password \
      --env MONGO_INITDB_DATABASE=events_db \
      mongo:4.4-bionic
  ```

- Connect

  ```bash
  # connect to mongodb instance using credentials
  mongo --host localhost --port 27017 --username root --password password --authenticationDatabase admin

  # use events_db and show events
  use events_db
  show collections
  db.events.find().pretty()
  ```

- Cleanup

  ```bash
  # Remove MongoDb container
  docker container rm mongo-1 --force

  # Remove docker network
  docker network rm mongo-net

  # Remove database files (may need sudo)
  rm -r ~/mongo-stack
  ```

#### Example 6

Host a single node MongoDb instance using _docker-compose_. Use environment variables to specify authentication information and initial database. Specify a javascript file that will be executed as part of setup and initialization (uses entry point scripts)

- Prepare javascript file

  ```bash
  # Create scripts folder
  mkdir -p ~/mongo-stack/scripts

  # Create events.js in scripts folder
  cat << EOF > ~/mongo-stack/scripts/events.js
  db.events.insertOne({
        "_id": "5f66c8ddba2c5ed57bd10c62",
        "name": "mollit",
        "isActive": true,
        "address": "300 Independence Avenue, Bedias, Virgin Islands, 3014",
        "about": "Et officia mollit fugiat ut amet. Cupidatat officia commodo est sit nostrud ea quis tempor. Tempor ex proident minim tempor labore proident laborum id excepteur.",
        "date": "Friday, August 30, 2019 6:09 PM",
        "latitude": "67.018372",
        "longitude": "138.780409",
        "tags": [
            "eiusmod",
            "officia",
            "deserunt",
            "nostrud",
            "nulla"
        ]
    });
  EOF
  ```

- Define stack

  ```yaml
  version: "3.7"
  services:
    mongo-1:
      image: mongo:4.4-bionic
      container_name: mongo-1
      restart: unless-stopped
      ports:
        - 27017:27017
      networks:
        - default
      environment:
        - MONGO_INITDB_ROOT_USERNAME=root
        - MONGO_INITDB_ROOT_PASSWORD=password
        - MONGO_INITDB_DATABASE=events_db
      volumes:
        - type: volume
          source: mongo-data-db
          target: /data/db
        - type: bind
          source: ~/mongo-stack/scripts/events.js
          target: /docker-entrypoint-initdb.d/events.js
          read_only: true
  networks:
    default:
      name: mongo-net
      driver: bridge
  volumes:
    mongo-data-db:
  ```

- Run stack

  ```bash
  # Run MongoDb stack as a daemon
  docker-compose up -d
  ```

- Connect

  ```bash
  mongo --host localhost --port 27017 --username root --password password --authenticationDatabase admin

  # use events_db and show events
  use events_db
  show collections
  db.events.find().pretty()
  ```

- Delete stack

  ```bash
  # Remove MongoDb stack including the removal of volumes
  docker-compose down -v
  ```

### Host Using MongoDB Atlas

[MongoDB Atlas] is a _Data as a Service (DaaS)_ offering, and is hosted in the cloud. There is no installation of MongoDB required and a free tier is available.

To get started, signup for free by registering for a free tier account [here](https://www.mongodb.com/cloud/atlas). The free tier entitles you to 512MB storage.

Please review the [MongoDB Atlas Documentation] for more information.

Once you have registered and setup your MongoDB instance on _Atlas_, you will be presented with a dashboard resembling the following image:

![atlas](https://user-images.githubusercontent.com/33935506/37251882-d19a3818-2520-11e8-97f6-7015435f3cbd.png)

---

## MongoDB Clients

The following MongoDB clients are popular choices for managing and/or interacting with MongoDB database:

- MongoDB Shell
- MongoDB Atlas
- NoSqlBooster
- Robo3T
- Visual Studio Code

### MongoDB shell

The MongoDB shell is the default way of interacting with MongoDB databases.

```bash
# connect to MongoDB
mongo --host localhost --port 27017

# show Current Database
db

# List Available Databases
show dbs

# create Database
use message_db

# create Collection
db.messages.insert({ message: 'hello mongodb' })

# list Collections
show collections
```

### MongoDB Compass

[MongoDB Compass] is a Graphical User Interface (GUI) tool that allows for the exploration of  MongoDB data.

- Freely available
- It is cross platform and is available for Linux, Windows, and Mac
- It supports the following primary features:

  - Run ad hoc queries
  - Perform CRUD operations on data
  - View and optimize query performance

In the following screenshot, I am connected to MongoDB running on my local machine, using MongoDB Compass.

![compass](https://user-images.githubusercontent.com/33935506/37252219-1e877398-2526-11e8-998a-6ecd6bcacaed.png)

For more information on MongoDB Compass, go [here](https://www.mongodb.com/products/compass)

### NoSqlBooster

[NoSqlBooster] is a Graphical User Interface (GUI) that provides an easy to use interface to work with your MongoDB database.

- It has a free addition available
- It is cross platform and is available for Linux, Windows, and Mac
- It provides the following features:

  - Shell extensions
  - Fluent Query API for MongoDB
  - Query MongoDB with SQL
  - Use Node Modules in your script

In the following screenshot, I am connected to MongoDB running on Docker, using NoSqlBooster.

![nosqlbooster](https://user-images.githubusercontent.com/33935506/37252401-18e5b4a6-2529-11e8-940f-9ad49aa82a4a.png)

For more information, please visit the [official NoSqlBooster website](https://nosqlbooster.com/).

### Robo 3T

[Robo 3T] (formerly RoboMongo), is a free GUI tool that can be used to explore your MongoDB databases.

- It is free to use.
- It is cross platform and is available for Linux, Windows, and Mac
- It provides a MongoDB GUI with embedded shell

In the following screenshot, I am connected to a local instance of MongoDB using Robo 3T:

![robo3t](https://user-images.githubusercontent.com/33935506/37252488-3fe78308-252a-11e8-94a7-36c528dca556.png)

[Robo 3T] can be downloaded [here](https://robomongo.org/download)

For more information, go [here](https://robomongo.org/).

### Visual Studio Code

There are a number of Visual Studio Code extensions available for MongoDB. The following 2 extensions are great options:

- Azure Databases

  - [Market Place](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
  - [Github](https://github.com/microsoft/vscode-cosmosdb)

- MongoDB For VS Code

  - [Market Place](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)
  - [Github](https://github.com/mongodb-js/vscode)

---

## Basic MongoDB Commands

Once you have your MongoDB instance running or hosted, open a mongo shell to your MongoDB server. For the purposes of this demonstration, I will be using my local installation 'localhost'.

```bash
# connect to mongodb instance
mongo --host localhost --port 27017

# connect to specific database on mongodb instance
mongo admin --host localhost --port 27017

# create a 'zips_db' database
use zips_db

# create 'zips' collection
db.zips.insertMany([
     { "city" : "AGAWAM", "loc" : [ -72.622739, 42.070206 ], "pop" : 5338, "state" : "MA", "_id" : "01001" },
     { "city" : "CUSHMAN", "loc" : [ -72.51564999999999, 42.377017 ], "pop" : 36963, "state" : "MA", "_id" : "01002" },
     { "city" : "BARRE", "loc" : [ -72.10835400000001, 42.409698 ], "pop" : 4546, "state" : "MA", "_id" : "01005" },
     { "city" : "BELCHERTOWN", "loc" : [ -72.41095300000001, 42.275103 ], "pop" : 10579, "state" : "MA", "_id" : "01007" },
     { "city" : "BLANDFORD", "loc" : [ -72.936114, 42.182949 ], "pop" : 240, "state" : "MA", "_id" : "01008" },
     { "city" : "BRIMFIELD", "loc" : [ -72.188455, 42.116543 ], "pop" : 706, "state" : "MA", "_id" : "01010" },
     { "city" : "CHESTER", "loc" : [ -72.988761, 42.279421 ], "pop" : 688, "state" : "MA", "_id" : "01011" },
     { "city" : "CHESTERFIELD", "loc" : [ -72.833309, 42.38167 ], "pop"  177, "state" : "MA", "_id" : "01012" },
     { "city" : "CHICOPEE", "loc" : [ -72.607962, 42.162046 ], "pop" : 3396, "state" : "MA", "_id" : "01013" },
     { "city" : "CHICOPEE", "loc" : [ -72.576142, 42.176443 ], "pop" : 1495, "state" : "MA", "_id" : "01020" }
   ])



# Read Operations

# get total collection count
db.zips.count()

# get all zip documents
db.zips.find().pretty()

# get 5 zip documents
db.zips.find().limit(5).pretty()

# get zip having a city name of 'BLANDFORD'
db.zips.find({"city": "BLANDFORD"})

# get zip having a city name of 'BLANDFORD', ignore case
db.zips.find({"city": { $regex: /blandford/i } })

# get zip having a city names of 'BLANDFORD' and 'BRIMFIELD'
db.zips.find({"city": { $in: ["BLANDFORD", "BRIMFIELD"] } }).pretty()

# get zip having a city names of 'BLANDFORD' and 'BRIMFIELD', ignore case
db.zips.find({"city": { $in: [ /blandford/i, /brimfield/i] } }).pretty ()

# get zip documents having a population greater than or equal to 30000
db.zips.find({"pop": { $gte: 30000}}).pretty()

# get all zip documents and use projection to only display city and population
db.zips.find({}, { "city": 1, "pop": 1, "_id": 0 })

# get all zip documents and use projection to only display city and population. Sort documents by city name in descending order
db.zips.find({}, { "city": 1, "pop": 1, "_id": 0 }).sort({"city": -1})



# Delete Operations

# delete all cities called 'CHICOPEE'. Ignore case
db.zips.remove({ "city": { $regex: /chicopee/i }})



# Update Operations

# update the city of 'BLANDFORD' by setting its population to 10
db.zips.updateOne({"city": "BLANDFORD"}, { $set: { "pop": 10}})
```

---

## Conclusion

For more information on MongoDB, please see the following curated list of resources:

- **Official Documentation**
  - [MongoDB Docs] - The official MongoDB documentation

- **Online Training**
  - [MongoDB University] - Free Online Classes on MongoDB from MongoDB
  - [MongoDB - The Complete Developer's Guide](https://www.udemy.com/course/mongodb-the-complete-developers-guide/) (by Academind)
  - [The Complete Developers Guide to MongoDB](https://www.udemy.com/course/the-complete-developers-guide-to-mongodb/) (by Stephen Grider)

- **Books**
  - [MongoDB: The Definitive Guide, 3rd Edition](https://www.oreilly.com/library/view/mongodb-the-definitive/9781491954454/) (by Shannon Bradshaw, Eoin Brazil, Kristina Chodorow)
  - [Learn MongoDB 4.x](https://www.oreilly.com/library/view/learn-mongodb-4x/9781789619386/) (by Doug Bierer)
  - [MongoDB Applied Design Patterns](https://www.oreilly.com/library/view/mongodb-applied-design/9781449340056/) (by Rick Copeland)
  - [MongoDB Recipes: With Data Modeling and Query Building Strategies](https://www.oreilly.com/library/view/mongodb-recipes-with/9781484248911/) (by Subhashini Chellappan, Dharanitharan Ganesan)

- **Certifications**
  - [MongoDB Professional
Certification Program](https://university.mongodb.com/certification)



---
[MongoDB Docs]: https://docs.mongodb.com
[MongoDB University]: https://university.mongodb.com
[MongoDB: The Definitive Guide, 3rd Edition (Early Release)]: http://shop.oreilly.com/product/0636920049531.do
[Docker]: https://www.docker.com
[Docker Guide]:(https://github.com/drminnaar/guides/tree/master/docker-guide)
[Docker Cheatsheet]: https://github.com/drminnaar/cheatsheets/blob/master/docker-cheatsheet.md
[MongoDB Atlas]: https://www.mongodb.com/cloud/atlas
[MongoDB Documentation]: https://docs.atlas.mongodb.com
[Daas]: https://en.wikipedia.org/wiki/Data_as_a_service
[MongoDB Compass]: https://www.mongodb.com/products/compass
[NoSqlBooster]: https://nosqlBooster.com
[Robo 3T]: https://robomongo.org
[Azure Databases Extension]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb
[Windows Subsystem For Linux (WSL)]: https://docs.microsoft.com/en-us/windows/wsl/about
[Windows Subsystem For Linux 2 (WSL2)]: https://docs.microsoft.com/en-us/windows/wsl/about#what-is-wsl-2
