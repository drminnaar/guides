# Alpine Linux Example

In this example, I run an Alpine Linux container.

## Run an [Alpine Linux] container (a lightweight linux distribution)

1. Find image and display brief summary

   ```docker
   docker search alpine --filter=stars=1000 --no-trunc
   ```

1. Fetch alpine image from Docker registry and save it

   ```docker
   docker pull alpine
   ```

1. Display list of local images

   ```docker
   docker image ls
   ```

1. List container contents using _listing_ format

   ```docker
   docker run alpine ls -l
   ```

1. Print message from container

   ```docker
   docker run alpine echo "Hello from Alpine!"
   ```

1. Running the run command with the -it flags attaches container to an interactive tty

   ```docker
   docker run -it alpine bin/sh
   ```