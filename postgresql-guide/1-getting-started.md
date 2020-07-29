![PostgreSQL Guide](https://dev-to-uploads.s3.amazonaws.com/i/n77s83wl9mvoogk4362q.png)

# Postgres Guide

This _Postgres Guide_ is an overview of PostgreSQL and how to get started. The topics covered in this guide are as follows:

- [Installation](#installation)
- [Cloud Hosting](#cloud-hosting)
- [Tools](#tools)
- [Naming Conventions](#naming-conventions)
- [Next Steps](#next-steps)

## Who is this Guide For

This guide is mostly for beginners. However, there may be something of interest for anyone even partly interested in PostgreSQL. For example:

- you may be interested in alternative ways to running Postgres locally. I provide 5 ways of running Postgres in your local development environment
- perhaps you've been wondering about the different cloud hosting options available. I mention a few well known options, but also some lesser known options
- if tooling is your interest, I mention a few of my favourites
- I provide a few naming conventions that I personally like to use

---

## Installation

### Windows 10

#### Using The Installer

As a general starting point, head over to the [Postgres Windows Installers page](https://www.postgresql.org/download/windows/). This is the current recommended approach and uses the installers from [EnterpriseDB](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads). The installer offers the following options:

![enterprisedb-setup](https://user-images.githubusercontent.com/33935506/87863930-604acd80-c9b5-11ea-9fdd-93dd11e9aaef.png)

If for example, you only want a Postgres client to connect to a hosted Postgres database (cloud, docker, etc), you would choose the options _pgAdmin 4_ and _Command Line Tools_.

#### Using Chocolatey

[Chocolatey] is software management automation for Windows that wraps installers, executables, zips, and scripts into compiled packages.

You can find [a list of Postgres packages here](https://chocolatey.org/search?q=postgres)

If for example you would like to install Postgres 12, you would use the [Chocolatey] package manager called _"choco"_ to install the package as follows:

```bash
choco install postgresql12
```

You could also install the [pgAdmin](https://chocolatey.org/packages/pgadmin4) tool separately as follows:

```bash
choco install pgadmin4
```

### Windows 10 WSL2 (Ubuntu 20.04)

Hopefully you've at least heard of [Windows Subsystem For Linux]. The [Windows Subsystem for Linux] lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup.

My suggestion is to use [Windows Subsystem For Linux 2]. WSL 2 is a new version of the architecture in WSL that changes how Linux distributions interact with Windows. It results in a more natural experience in terms of working with Linux distributions within Windows. WSL 2 is only available in Windows 10, Version 2004, Build 19041 or higher.

Assuming that you have WSL or WSL2 installed and operational, install the _Ubuntu 20.04_ distribution via the _Microsoft Store_.

![ubuntu-wsl2](https://user-images.githubusercontent.com/33935506/87864118-65f5e280-c9b8-11ea-8edd-51cce0aaae18.png)

#### Open Terminal And Install

Open up the WSL2 Ubuntu 20.04 terminal and type the following commands to install Postgres 12.

```bash
# Step 1 - Update packages and ensure that wget is installed
sudo apt update
sudo apt -y install wget
sudo apt -y upgrade

# Step 2 - Import GPG key and add Postgres 12 to Ubuntu repository
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# Step 3 - Add Postgres repository contents
echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list

# Step 4 - Update and install Postgres 12
# If you only require the client tools, skip to Step 5
sudo apt update
sudo apt -y install postgresql-12

# Step 5 - Update and install Postgres 12 Client
sudo apt update
sudo apt -y install postgresql-client-12

# Step 6 - Verify installation

# should display version
psql --version

# should prompt you for password and list databases
psql -h localhost -U postgres -l
```

### Ubuntu 20.04

You can follow the exact same steps as in WSL2 Ubuntu 20.04.

Open a terminal and execute the following commands.

```bash
# Step 1 - Update packages and ensure that wget is installed
sudo apt update
sudo apt -y install wget
sudo apt -y upgrade

# Step 2 - Import GPG key and add Postgres 12 to Ubuntu repository
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# Step 3 - Add Postgres repository contents
echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list

# Step 4 - Update and install Postgres 12
# If you only require the client tools, skip to Step 5
sudo apt update
sudo apt -y install postgresql-12

# Step 5 - Update and install Postgres 12 Client
sudo apt update
sudo apt -y install postgresql-client-12

# Step 6 - Verify installation

# should display version
psql --version

# should prompt you for password and list databases
psql -h localhost -U postgres -l
```

### Docker

_Docker_ is a computer program that performs operating-system-level virtualization also known as containerization. In other words it allows one to containerize applications.

The assumption at this point is that you have [Docker] installed.

- [General OS Install](https://docs.docker.com/engine/install/)
- [Docker Install For Windows](https://docs.docker.com/docker-for-windows/install/)
- [Docker Install For Linux](https://docs.docker.com/engine/install/ubuntu/)

#### Docker Container

Regardless of whether you're using Linux, Windows, or WSL, the following commands are the same for all platforms:

```bash
# Step 1 - Run a Postgres container with the following attributes:
# - image: postgres:12-alpine (Postgres version 12 running on Alpine Linux)
# - name: pg-test
# - port: 5432
# - environment variable POSTGRES_PASSWORD: password
# - run as a daemon service

docker container run --name pg-test -p 5432:5432 -e POSTGRES_PASSWORD=password -d postgres:12-alpine

CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                           NAMES
589a52699b61        postgres:12-alpine   "docker-entrypoint.s…"   5 seconds ago       Up 3 seconds        0.0.0.0:5432->5432/tcp          pg-test
```

```bash
# Step 2 - Run the pSql client on the newly created Postgres container
# - connect to Postgres using postgres user
# - list available databases

docker container exec -it pg-test psql -U postgres -l

  Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
```

#### Docker Compose

_[Docker Compose]_ is a tool for defining and running multi-container Docker applications.

Running docker commands to host Postgres can become tedious very quickly. The Docker command can also becomes unwieldy with all the different parameters passed as arguments. Also, when one is required to run multiple containers, using the Docker _"run"_ compounds the aforementioend issues. It would also be nice to be able to bring up (and tear down) an environment as a "stack" of containers, volumes and networks. That's where _[Docker Compose]_ comes in.

##### Example 1

In the previous docker container CLI example where we run a Postgres instance in a docker container, we execute the following command:

```bash
# Run Postgres 12
docker container run --name pg-test -p 5432:5432 -e POSTGRES_PASSWORD=password -d postgres:12-alpine
```

We could replace that command with a _docker-compose.yml_ file that looks like the following:

```yaml
version: '3.5'
services:
  pg_test:
    container_name: pg_test
    image: postgres:12-alpine
    restart: always
    ports:
      - 5432:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=postgres
```

- Create a _docker-compose.yml_ file using your editor of choice in a sample folder
- Copy and paste the contents of the docker-compose description above into new _docker-compose.yml_ file
- Open a terminal in the location of the created _docker-compose_ file
- Run the following command:

  ```bash
  # execute docker-compose.yml file as daemon (-d)
  # - this command will start the services (containers) defined in the docker-compose.yml

  docker-compose up -d

  Creating pg_test ... done
  ```

- To stop and remove all services defined in the _docker-compose.yml_ file, run the following command:

  ```bash
  docker-compose down --volumes

  Stopping pg_test ... done
  Removing pg_test ... done
  Removing network pg_test_default
  ```

##### Example 2

In this next example we will define a Postgres environment by additionally specifying a named volume and network. We will also define an additional service to host the [pgAdmin] tool in a container.

- Define the docker-compose file:

  ```yaml
  version: '3.5'
  services:
    pg_test:
      container_name: pg_test
      image: postgres:12-alpine
      restart: always
      ports:
        - 5432:5432
      environment:
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=password
        - POSTGRES_DB=postgres
      volumes:
        - ./db/data:/var/lib/postgresql/data
      networks:
        - default
    pg_admin:
      container_name: pg_admin
      image: dpage/pgadmin4
      restart: always
      environment:
        - PGADMIN_DEFAULT_EMAIL=admin@example.com
        - PGADMIN_DEFAULT_PASSWORD=password
      ports:
        - 8080:80
      networks:
        - default
  networks:
    default:
      name: pg_net
  ```

- Execute the _docker-compose.yml_ file:

  ```bash
  # Start compose
  docker-compose up -d
  
  Creating network "pg_net" with the default driver
  Creating pg_admin ... done
  Creating pg_test  ... done
  
  # List containers
  docker container ls
  
  CONTAINER ID        IMAGE                COMMAND                    CREATED             STATUS              PORTS                           NAMES
  f31ed9231db5        postgres:12-alpine   "docker-entrypoint.s…"   4 minutes   ago       Up 4 minutes        0.0.0.0:6432->5432/tcp          pg_test
  25eba763f24b        dpage/pgadmin4       "/entrypoint.sh"         4 minutes   ago       Up 4 minutes        443/tcp, 0.0.0.0:8090->80/tcp   pg_admin
  
  # List containers using docker-compose
  docker-compose ps
  
  Name                Command              State               Ports
  --------------------------------------------------------------------------------
  pg_admin   /entrypoint.sh                  Up      443/tcp, 0.0.0.0:8090->80/tcp
  pg_test    docker-entrypoint.sh postgres   Up      0.0.0.0:6432->5432/tcp
  ```

- Bring down environment:

  ```bash
  docker-compose down --volumes

  Stopping pg_test  ... done
  Stopping pg_admin ... done
  Removing pg_test  ... done
  Removing pg_admin ... done
  Removing network pg_net
  ```

---

## Cloud Hosting

### AWS

Unfortunately, to create an AWS account, you require your credit card number :anguished:

If you already have an account or if you're willing to signup for a 12 month free tier subscription. [Try out AWS RDS Postgres](https://aws.amazon.com/rds/postgresql/)

For more information, see the following links

- [AWS Free Tier]
- [AWS Free Tier Database]

Another Postgres service offering that I've grown to appreciate is the [Aurora Postgresql].

According to the [official documentation](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.AuroraPostgreSQL.html):

> Amazon Aurora PostgreSQL is a fully managed, PostgreSQL-compatible, and ACID-compliant relational database engine that combines the speed and reliability of high-end commercial databases with the simplicity and cost-effectiveness of open-source databases. Aurora PostgreSQL is a drop-in replacement for PostgreSQL and makes it simple and cost-effective to set up, operate, and scale your new and existing PostgreSQL deployments, thus freeing you to focus on your business and applications. Amazon RDS provides administration for Aurora by handling routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair. Amazon RDS also provides push-button migration tools to convert your existing Amazon RDS for PostgreSQL applications to Aurora PostgreSQL. 

Another way of thinking about Aurora is as a "serverless Postgresql" database offering.

### Azure

Like AWS, you unfortunately also need a credit card to create an Azure account :anguished:

For more information about using Postgresql on Azure, please see the following links:

- [Azure Database for Postgres](https://azure.microsoft.com/en-us/services/postgresql/)
- [Official Documentations](https://docs.microsoft.com/en-us/azure/postgresql/)
- [Azure CLI Quickstart](https://docs.microsoft.com/en-us/azure/postgresql/quickstart-create-server-up-azure-cli)

### Heroku

[Heroku](https://www.heroku.com) is a platform as a service (PaaS) that enables developers to build, run, and operate applications entirely in the cloud.

#### Step 1 - Create Account

Create a [free account here](https://www.heroku.com/free). You won't need a credit card :smile:

#### Step 2 - Create Application

Once you have an account, create a new application by selecting 'New'

![0](https://user-images.githubusercontent.com/33935506/87867220-0ced7500-c9df-11ea-8d34-9164f9553891.png)

Select 'Create New App'

![1](https://user-images.githubusercontent.com/33935506/87867222-0e1ea200-c9df-11ea-8071-09f05785b035.png)

Give application a name. The name must be unique.

![2](https://user-images.githubusercontent.com/33935506/87867223-0eb73880-c9df-11ea-86ff-5ab5a9a5d2b1.png)

When your application is created, you will land on a screen similar to the following.

![3](https://user-images.githubusercontent.com/33935506/87867224-0eb73880-c9df-11ea-9480-7ade6057e098.png)

#### Step 3 - Add Postgres Database

Select 'Overview'. To add a Postgres database to your application, you need to configure an addon. Select 'Configure Addon'

![4](https://user-images.githubusercontent.com/33935506/87867225-0f4fcf00-c9df-11ea-921b-8ddf82e7e631.png)

Type in 'Postgres' and a list of addons will appear. Choose 'Heroku Postgres'.

![5](https://user-images.githubusercontent.com/33935506/87867226-0f4fcf00-c9df-11ea-8fe3-e02418c3745c.png)

Select the 'Hobby Dev - Free' option

![6](https://user-images.githubusercontent.com/33935506/87867227-0fe86580-c9df-11ea-90f1-a6743c9476a1.png)

Your database is created and a link to your database is shown. It may take a minute before the link works correctly. This is because your database is being provisioned. When ready, select the link to view database details.

![7](https://user-images.githubusercontent.com/33935506/87867228-1080fc00-c9df-11ea-9b41-430f137e5c68.png)

#### Step 4 - Connect to Database

Select 'Settings'

![8](https://user-images.githubusercontent.com/33935506/87867229-1080fc00-c9df-11ea-8de2-61150dc72ee3.png)

Select 'View Credentials'

![9](https://user-images.githubusercontent.com/33935506/87867230-11199280-c9df-11ea-94da-91c5cca36cc5.png)

Take note of the credential details that will be required to connect to your database.

![10](https://user-images.githubusercontent.com/33935506/87867231-11b22900-c9df-11ea-98c8-3196d47c8155.png)

Now that you have your credentials, you can open up the pSql command line tool and connect to your Heroku Postgres database.

```bash
psql -h ec2-00-000-000-00.compute-1.amazonaws.com -U xxxxvjtsxxxxxx -d d68ultexxxxxxx

# You should now be connected to your database
```

I think the free offering from Heroku is awesome. I've found it to be the easiest to work with from a hobby dev perspective.

### ElephantSql

[ElephantSql](https://www.elephantsql.com/) is a PostgreSQL database hosting service.

Provides a **free** pricing plan with the following attributes:

- Free
- No credit card required
- Shared high performance server
- 20 MB Data
- 5 concurrent connections

See more at the following links:

- [Official Website](https://www.elephantsql.com/)
- [Getting Started](https://www.elephantsql.com/docs/index.html)

#### Quickstart

##### Step 1 - Login

You can [signup and login here](https://customer.elephantsql.com/login). After login, you will land on the following view. Select 'Create New Instance'.

![0](https://user-images.githubusercontent.com/33935506/87868742-6197ec80-c9ed-11ea-967e-0addcfbf0c1b.png)

##### Step 2 - Create New Instance

Provide details like name, plan and tags. Select 'Select Region'.

![1](https://user-images.githubusercontent.com/33935506/87868744-63fa4680-c9ed-11ea-8b80-bbe1d999c978.png)

Choose preferred region. Then select 'Review'.

![2](https://user-images.githubusercontent.com/33935506/87868749-69579100-c9ed-11ea-825c-05b676b62cec.png)

Select 'Create Instance'

![3](https://user-images.githubusercontent.com/33935506/87868751-6d83ae80-c9ed-11ea-9829-a866f5d826bb.png)

Congratulations! You created your first hosted Postgres database. Select your database to view details.

![4](https://user-images.githubusercontent.com/33935506/87868755-72486280-c9ed-11ea-8339-4c16bf003d89.png)

##### Step 3 - Connect To Database

Take note of your database credentials.

![5](https://user-images.githubusercontent.com/33935506/87868760-75435300-c9ed-11ea-8d93-54a90714c02a.png)

```bash
psql -h ruby.db.elephantsql.com -U xxxxexxx -d xxxxexxx
```

---

## Tools

### pSql

As per the official [Postgres documentation](https://www.postgresql.org/docs/12/app-psql.html):

> psql is a terminal-based front-end to PostgreSQL. It enables you to type in queries interactively, issue them to PostgreSQL, and see the query results. Alternatively, input can be from a file or from command line arguments. In addition, psql provides a number of meta-commands and various shell-like features to facilitate writing scripts and automating a wide variety of tasks.

Be sure that you have the pSql command line tool installed:

- See [Windows 10 Install](#using-the-installer).
- See [Windows 10 WSL2 Install](#open-terminal-and-install)
- See [Ubuntu Install](#ubuntu-20.04)

Open a terminal and try the following commands to interact with Postgres.

```bash
# Check version
psql --version

# Get help
psql --help

# List databases
psql -h localhost -U postgres -l

# Connect to Postgres
# Connection options:
#   -h, --host=HOSTNAME      database server host or socket directory (default: "local socket")
#   -p, --port=PORT          database server port (default: "5432")
#   -U, --username=USERNAME  database user name
#   -w, --no-password        never prompt for password
#   -W, --password           force password prompt (should happen automatically)
psql -h localhost -U postgres -W

# Show help on backslash commands
\?

# List databases
\l

# List available views
\dv

# List users and roles
\du

# List available tables
\dt

# Describe a table
\d table_name

# List all schemas
\dn
```

For the next set of examples we will do the following:

- Create a database
- Create a schema
- Create a table
- Insert some data
- Query some data

```sql
-- Create Database
CREATE DATABASE test;

-- Change to 'test' database
\c test

-- Create schema
CREATE SCHEMA sales;

-- Create table
CREATE TABLE sales.consultant
(
  consultant_id SERIAL NOT NULL,
  created_date TIMESTAMPTZ NOT NULL,
  updated_date TIMESTAMPTZ NOT NULL,
  name TEXT NOT NULL, 
  CONSTRAINT "pk_sales_consultant_consultantid" PRIMARY KEY ("consultant_id")
);

-- Insert data
INSERT INTO sales.consultant (created_date, updated_date, name) VALUES
  (NOW(), NOW(), 'bob100'),
  (NOW(), NOW(), 'bob200'),
  (NOW(), NOW(), 'bob300'),
  (NOW(), NOW(), 'bob400'),
  (NOW(), NOW(), 'bob500'),
  (NOW(), NOW(), 'bob600'),
  (NOW(), NOW(), 'bob700'),
  (NOW(), NOW(), 'bob800'),
  (NOW(), NOW(), 'bob900'),
  (NOW(), NOW(), 'bob1000');

-- Query data
SELECT * FROM sales.consultant;

-- Query data with timing enabled
\timing
SELECT * FROM sales.consultant;

-- Show tables in sales schema
\dt sales.
```

### pgAdmin

_[pgAdmin](https://www.pgadmin.org/)_ is a web based database management tool for managing Postgresql databases.

Try the [online version](https://www.pgadmin.org/try/). Please take note that you may sometimes need to refresh page for things to work :)

#### Download

Find everything you need to download and install _pgAdmin_ at the [official pgAdmin download page](https://www.pgadmin.org/download/)

If you're on Windows and have [Chocolatey] installed, you can install pgAdmin using the following command.

```bash
choco install pgadmin4
```

Find out more at the [pgAdmin Chocolatey page](https://chocolatey.org/packages/pgadmin4)

![8](https://user-images.githubusercontent.com/33935506/88260765-295c1b00-cd19-11ea-86c7-112873c1356a.png)

#### Quickstart

##### Step 1 - Open pgAdmin

![1](https://user-images.githubusercontent.com/33935506/88258401-362a4000-cd14-11ea-91a8-b7d9cdfd1acf.png)

##### Step 2 - Create a Server Group

Right click on 'Servers' and select 'Server Group'.

![2](https://user-images.githubusercontent.com/33935506/88258402-375b6d00-cd14-11ea-8fb6-7aa5c83348ee.png)

##### Step 3 - Create a Server

Right click on 'Test' server group and select 'Create' -> 'Server'

![3](https://user-images.githubusercontent.com/33935506/88258403-375b6d00-cd14-11ea-8c0a-dd79de98006f.png)

Specify general server details like:

- Name
- Server Group
- Background Color

![4](https://user-images.githubusercontent.com/33935506/88258404-37f40380-cd14-11ea-884a-ebe1ef61d094.png)

Specify database connection details like:

- Host
- Port
- Username
- Password

![5](https://user-images.githubusercontent.com/33935506/88258405-388c9a00-cd14-11ea-9885-e4e16fed6a8d.png)

You should now be connected!

![6](https://user-images.githubusercontent.com/33935506/88258407-388c9a00-cd14-11ea-8033-a354c6ceb460.png)

##### Step 5 - Execute Query

Select the highlighted (red circle) 'Query Tool' option.

Specify a SQL query and execute

![7](https://user-images.githubusercontent.com/33935506/88258408-39253080-cd14-11ea-9c3d-271a083749ab.png)

### Azure Data Studio

According to the [official documentation](https://docs.microsoft.com/en-us/sql/azure-data-studio/what-is):

> Azure Data Studio is a cross-platform database tool for data professionals using the Microsoft family of on-premises and cloud data platforms on Windows, MacOS, and Linux.

Azure Data Studio highlights:

- Free
- Cross platform (Windows, Linux and MacOS)
- Intellisense
- Smart code snippets
- Customizable Server and Database Dashboards
- Connection Management (Server Groups)
- Integrated terminal
- Notebooks

Find more information at he following links:

- [What is Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/what-is)
- [Quickstart](https://docs.microsoft.com/en-us/sql/azure-data-studio/quickstart-postgres)

#### Download

There are [detailed install instructions](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio) that can be used to install _Azure Data Studio_ on Windows, Linux, and MacOS.

If you're on Windows, you can also use [Chocolatey](https://chocolatey.org) to install the [Azure Data Studio chocolatey package](https://chocolatey.org/packages/azure-data-studio)

#### Quickstart

##### Step 1 - Connect

Open Azure Data Studio and select the menu option to create a new connection.

![0](https://user-images.githubusercontent.com/33935506/88033768-dc027100-cb93-11ea-87c3-e15132914c8b.png)

Provide the details of your Postgres database.

![1](https://user-images.githubusercontent.com/33935506/88033769-dd339e00-cb93-11ea-9936-22e9b7dad0d0.png)

Now that you're connected, you should see something that looks like the following

![2](https://user-images.githubusercontent.com/33935506/88033770-dd339e00-cb93-11ea-8010-e34c6953979b.png)

##### Step 2 - Run a query

Select the option to create a new query window. Start typing your query. Notice the intellisense.

![3](https://user-images.githubusercontent.com/33935506/88033774-ddcc3480-cb93-11ea-9ace-677d418d09f4.png)

Execute your query and you get something that looks as follows.

![4](https://user-images.githubusercontent.com/33935506/88033775-de64cb00-cb93-11ea-8f6c-e6e85188589f.png)

#### Theming

This isn't a tutorial on _Azure Data Studio_ but thought I'd mention that I am using _"Atom One Dark"_ as my theme.

![5](https://user-images.githubusercontent.com/33935506/88034386-bd50aa00-cb94-11ea-8456-9dc9884ca6b0.png)

Just like _Visual Studio Code_, it is possible to download different themes for _Azure Data Studio_.

### DBeaver

According to the [DBeaver Website](https://dbeaver.io/),

> DBeaver is a free multi-platform database tool for developers, database administrators, analysts and all people who need to work with databases. Supports all popular databases: MySQL, PostgreSQL, SQLite, Oracle, DB2, SQL Server, Sybase, MS Access, Teradata, Firebird, Apache Hive, Phoenix, Presto

Azure Data Studio highlights:

- Free
- Cross platform (Windows, Linux and MacOS)
- Intellisense

Please see the [official documentation](https://github.com/dbeaver/dbeaver/wiki)

#### Download

Find the [official DBeaver download here](https://dbeaver.io/download/)

If you're on Windows and have [Chocolatey] installed, you can install _DBeaver_ using the following command.

```bash
choco install dbeaver
```

Find out more at the [DBeaver Chocolatey page](https://chocolatey.org/packages/dbeaver)

![1](https://user-images.githubusercontent.com/33935506/88038055-d27c0780-cb99-11ea-9504-38a69a91b6c4.png)

#### Quickstart

##### Step 1 - Open DBeaver

![0](https://user-images.githubusercontent.com/33935506/88038052-d14ada80-cb99-11ea-947b-48430c7ca23d.png)

![2](https://user-images.githubusercontent.com/33935506/88038057-d3149e00-cb99-11ea-87b4-2ce93100c9ad.png)

##### Step 2 - Connect to database

Select option to create new connection. Select the Postgres connection type.

![3](https://user-images.githubusercontent.com/33935506/88038059-d3149e00-cb99-11ea-8c13-b9884bb6b8c7.png)

Provide Postgres database connection details.

![4](https://user-images.githubusercontent.com/33935506/88038061-d3ad3480-cb99-11ea-84dc-a307d2731dd9.png)

##### Step 3 - Run a query

Open a new query window and type some SQL. Take note of the intellisense.

![5](https://user-images.githubusercontent.com/33935506/88038062-d3ad3480-cb99-11ea-95ce-1227a1c0be52.png)

The query results are shown as follows.

![6](https://user-images.githubusercontent.com/33935506/88038064-d445cb00-cb99-11ea-8e39-257df91d178e.png)

##### Step 4 - Create ERD diagram

One of the features that I love about DBeaver is the ability to easily generate ERD (Entity Relationship Diagram) diagrams from the schema. In the following screens I use the sample database that comes with DBeaver to generate an ERD.

![7](https://user-images.githubusercontent.com/33935506/88038067-d445cb00-cb99-11ea-8a14-3a6c5ee8a312.png)

![8](https://user-images.githubusercontent.com/33935506/88038071-d4de6180-cb99-11ea-9dc3-19c3fab9d3d4.png)

### Visual Studio Code

According to official documentation,

> Visual Studio Code is a source code editor developed by Microsoft for Windows, Linux and macOS. It includes support for debugging, embedded Git control, syntax highlighting, intelligent code completion, snippets, and code refactoring.

There are many SQL extensions for VSCode. I like [PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-ossdata.vscode-postgresql).

Install the [PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-ossdata.vscode-postgresql) extension. Find more information [here](https://github.com/Microsoft/vscode-postgresql).

![4](https://user-images.githubusercontent.com/33935506/88045104-7073d000-cba2-11ea-9b86-903bd239d0dd.png)

Once the extension is installed, follow these instructions (taken from extension quickstart):

- Open the Command Palette (Ctrl + Shift + P).
- Search and select 'PostgreSQL: New Query'
- In the command palette, select 'Create Connection Profile'. Follow the prompts to enter your Postgres instance's hostname, database, username, and password.

![0](https://user-images.githubusercontent.com/33935506/88042916-cfd0e080-cba0-11ea-8f9f-0146c505438d.png)

![1](https://user-images.githubusercontent.com/33935506/88042930-d4959480-cba0-11ea-966b-0681aaf87923.png)

![2](https://user-images.githubusercontent.com/33935506/88042938-d65f5800-cba0-11ea-9983-8a61d31fc3b7.png)

![3](https://user-images.githubusercontent.com/33935506/88042943-d7908500-cba0-11ea-8cf5-7b5592cc5203.png)

---

## Naming Conventions

This is an opinionated section on my preferences in terms of Postgres database naming conventions. 

I use _[Snake case]_ for **ALL** naming. [Snake case] (for example: snake_case), is a style of writing a word, having spaces, where each space is replaced by an underscore (_) character. Also, the first letter of each word is written in lowercase.

| Database               | Schema | Table    | Columns      | Keys                                         | Indexes                                             |
|:-----------------------|--------|:---------|:-------------|----------------------------------------------|-----------------------------------------------------|
| example_onlinestore_db | sales  | customer | customer_id  | pk_sales_customer_customerid (primary key)   | ifk_sales_customer_consultantid (foreign key index) |
|                        |        |          | created_date | fk_sales_customer_consultantid (foreign key) | ux_sales_customer_email (unique index)              |
|                        |        |          | updated_date |                                              | ix_sales_customer_createddate (index)               |
|                        |        |          | first_name   |                                              |                                                     |
|                        |        |          | last_name    |                                              |                                                     |
|                        |        |          | email        |                                              |                                                     |

```sql
-- Create database
CREATE DATABASE example_onlinestore_db;

-- Create sales schema
CREATE SCHEMA sales;

-- Create consultant table
CREATE TABLE sales.consultant
(
  consultant_id SERIAL INT NOT NULL,
  created_date TIMESTAMPTZ NOT NULL,
  updated_date TIMESTAMPTZ NOT NULL,
  first_name TEXT NOT NULL,
  last_name TEXT NOT NULL,
  email TEXT NOT NULL,
  CONSTRAINT "pk_sales_consultant_consultantid" PRIMARY KEY ("consultant_id")
)

-- Create unique index
CREATE UNIQUE INDEX "ux_sales_consultant_email" ON TABLE sales.consultant ("email");

-- Create customer table
CREATE TABLE sales.customer
(
  customer_id SERIAL INT NOT NULL,
  created_date TIMESTAMPTZ NOT NULL,
  updated_date TIMESTAMPTZ NOT NULL,
  first_name TEXT NOT NULL,
  last_name TEXT NOT NULL,
  email TEXT NOT NULL,
  consultant_id INT NOT NULL,
  CONSTRAINT "pk_sales_customer_customerid" PRIMARY KEY ("customer_id")
);

-- Create foreign key
ALTER TABLE sales.customer 
ADD CONSTRAINT "fk_sales_customer_consultantid"
FOREIGN KEY ("consultant_id") REFERENCES sales.consultant ("consultant_id")
ON DELETE NO ACTION ON UPDATE NO ACTION;

-- Create index on foreign key
CREATE INDEX "ifk_sales_customer_consultantid" ON TABLE sales.customer ("consultant_id");

-- Create unique index
CREATE UNIQUE INDEX "ux_sales_customer_email" ON TABLE sales.customer ("email");

-- Create index
CREATE INDEX "ix_sales_customer_createddate" ON TABLE sales.customer ("created_date");
```

---

## Next Steps

In this guide I've highlighted some of the areas that I think are important to know about PostgreSQL at a high level. If you would like to learn more about PostgreSQL, please have a look at the following resources:

- [Postgres Tutorial](https://www.postgresqltutorial.com/)
- [Postgres Guide](http://postgresguide.com/)
- [Postgres Exercises](https://pgexercises.com/questions/basic/)
- [Awesome Postgres](https://github.com/dhamaniasad/awesome-postgres) - A curated list of awesome PostgreSQL software, libraries, tools and resources, inspired by awesome-mysql

I have also created 2 projects on Github that may be of interest:

- [Jukenook Db] - a database reference project that demonstrates how to manage a PostgreSQL database in development using Docker and Flyway.
- [Flyway Guide] - A starting point to learn about how to use [Flyway] to manage Postgres database versioning and migrations.

---

[Chocolatey]: https://chocolatey.org/
[Windows Subsystem For Linux 2]: https://docs.microsoft.com/en-us/windows/wsl/wsl2-index
[Windows Subsystem For Linux]: https://docs.microsoft.com/en-us/windows/wsl/about
[Docker]: https://www.docker.com
[Docker-Compose]: https://docs.docker.com/compose
[Docker Compose]: https://docs.docker.com/compose
[pgAdmin]: https://www.pgadmin.org
[AWS Free Tier Database]: https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=tier%2312monthsfree&awsf.Free%20Tier%20Categories=categories%23databases
[AWS Free Tier]: https://aws.amazon.com/free
[Aurora Postgresql]: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html
[Snake case]: https://en.wikipedia.org/wiki/Snake_case
[Jukenook Db]: https://github.com/drminnaar/jukenook-db
[Flyway Guide]: https://github.com/drminnaar/guides/tree/master/flyway-guide
[Flyway]: https://flywaydb.org/
