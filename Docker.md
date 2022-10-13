# Docker

## Overview

This content is intended as a summary introduction for developers that may need to work with and support Docker-based applications in production, by guiding users to other information resources and suggesting a sensible order in which to learn things.

## External learning references

In addition to this page, the following links may contain useful or more-detailed content for learning Docker:

* <https://docker-curriculum.com>
* <https://docs.linuxserver.io/general/containers-101>

# Docker terms and concepts

## What is Docker, and what is a Container?

[Docker](https://opensource.com/resources/what-docker) is a open source computer program that performs operating-system-level virtualisation (also known as containerisation). Docker allows us to deploy and run software applications inside **Containers**, which (roughly) are applications that are packaged up with all the assets needed to run (binaries, libraries, static files, etc.) on any  suitable host that runs the Docker daemon. A Container is (approximately) an executable which is a kind of extremely lightweight VM without including any OS of its own. In effect, Docker provides a set of APIs that a Container can use to access the underlying OS  functions.

Resources:

* What is a container: <https://www.docker.com/what-container>

## What is an Image?

An **Image** (in the context of Docker) is an inert, immutable file that is a snapshot of a container. Images are stored in a **Registry** (see later), and are (roughly) equivalent to a tagged Git commit of a Container. Images are built from instructions in a **Dockerfile** (see below).

Source: <https://stackoverflow.com/questions/23735149/what-is-the-difference-between-a-docker-image-and-a-container>

## What is a Dockerfile?

A Dockerfile is a text document that contains all of the commands a user would call on the command line to assemble an Image. An analogy: a Dockerfile is like a cake recipe, an Image is a pre-made cake mix, a Container is a finished cake.

Resources:

* Dockerfile specification reference: <https://docs.docker.com/engine/reference/builder/>

## What is a Swarm?

Docker can be run on more than one host which can be made aware of other Docker hosts in a "Swarm". Each host is called a "node", and  nodes can be authorised to manage and control other nodes (or not). Swarm is a clustering and management tool for Docker containers.Currently the Department is not running production applications in a multi-node Swarm, so we don't need to know much more about thisfeature of Docker. We do make use of single-node Swarms to use the features related to application Services and Stacks (see below).

## What is a Service, and what is a Stack?

A Service is one or more containers that are running the same version of a Container. A Stack is a collection of Services that make up an application in a specific environment.

Resources:

* <https://docs.docker.com/get-started/part3/>

## What is a Registry?

A Docker registry is a storage and content delivery system for named Docker images. It can be self-hosted, third-party-hosted, or the  official Docker Hub registry (<https://hub.docker.com/>).

# Docker command examples

## Container creation and usage

Pull an image from the online registry to local storage:

```bash
docker pull <IMAGE NAME>
```

Basic run commands:

```bash
docker container run <IMAGE>
docker container run --publish 80:80 nginx
docker container run --detach --publish 8281:8080 --env DATABASE_URL=postgres://username:PASSWORD@hostname.lan.fyi/database_name --env DEBUG=True dbcawa/prs:latest
```

Breakdown:

* Searches Docker Hub for an image called "nginx" and downloads the latest one (caches it locally).
* Creates a new container based on the image.
* Gives it a virtual IP on a private network in the Docker engine.
* Open port 80 on the host, and forward that to port 80 inside the container.
* Runs the container in the foreground.

```bash
docker container run --publish 80:80 --detach nginx
```

Same as above, but detached. Stop a container:
```bash
docker container stop <CONTAINER ID>
```

List containers:
```bash
docker container ls
docker container ls -a
```

Give it a name ("webhost"):
```bash
docker container run --publish 80:80 --detach --name webhost nginx
```

View logs, use top and stop it:
```bash
docker container logs <NAME>
docker container top <NAME>
docker container inspect <NAME>
docker container stop <NAME>
```

Remove containers:
```bash
docker container ls -a
docker container rm <ID1> <ID2>
docker container rm -f <ID OF RUNNING CONTAINER>
```

Manage containers assignment (command list):
```bash
docker container run --publish 8082:80 --detach --name webserver httpd
docker container logs webserver
docker container run --publish 3306:3306 -d -n mysql_db -e MYSQL_RANDOM_ROOT_PASSWORD=true
docker container run --publish 3306:3306 -d --name mysql_db -e MYSQL_RANDOM_ROOT_PASSWORD=true
docker container run --publish 3306:3306 --detach --name mysql_db -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
docker image ls
docker container ls
docker container rm -f 53b 13f 4c3
docker container ls -a
```

Viewing information about running containers:
```bash
docker container top <NAME>
docker container inspect <NAME>
docker container stats
```

Getting inside a running container:
```bash
# Start a container interactively and run sh in it:
docker container start -it alpine sh
# Run a bash shell on a running container:
docker container exec -it <NAME> bash
```

Misc. command dump:
```bash
# Display IP address of a running container:
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' <NAME>
# Remove all exited containers:
docker ps -a | grep Exit | cut -d ' ' -f 1 | xargs docker rm
```

Docker container networking: <https://docs.docker.com/network/>
```bash
docker network ls
docker network inspect <NAME>
docker network create <NAME>
docker network connect <NETWORK> <CONTAINER>
docker network disconnect <NETWORK> <CONTAINER>
```

## Container lifetime & persistent data
* Containers are usually immutable and ephemeral.
* Non-ephemeral data (databases, uploaded files, etc.) needs to be persisted. It is more important than the container itself.
* Data can be persisted using volumes and bind mounts.

Define a named volume when starting a container:
```bash
docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
docker container run -d --name pgdb -e POSTGRES_PASSWORD=pass -p 5433:5432 -v pgdb:/var/lib/postgresql/data postgres:10-alpine
```

Bind mounting maps a host file/dir to a container file/dir. Can't use this in a Dockerfile, must be at container run. E.g.:
```bash
docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v /path/on/host:/path/in/container mysql
```

# Docker Swarm

Swarm mode uses a distributed consensus to maintain cohesion. Raft consensus overview: <http://thesecretlivesofdata.com/raft/>

Docs, deploying services to a swarm: <https://docs.docker.com/engine/swarm/services/>

Creating a swarm using overlay network driver creates a distributed network between all the Docker hosts using that network (kind of a VPN between the hosts).

Reference: <https://docs.docker.com/network/overlay/>

Swarm services connected to the same overlay network expose all ports to each other. For a port to be accessible outside of the  service, it must be published on service create/update. All nodes in a swarm participate in an ingress routing mesh. The mesh enables  each node in the swarm to accept connections on published ports to any service running in the swarm. Routing mesh docs: <https://docs.docker.com/engine/swarm/ingress/>

The routing mesh:

* Routes ingress (incoming) network packets for a service to the proper task.
* Spans all nodes in the swarm.
* Load balances swarm services across their tasks.

To enable Swarm mode for a Docker host:
```bash
docker swarm init
docker info
```

Service commands:
```bash
docker service create alpine ping 1.1.1.1
docker service ls
docker service update <NAME> --replicas 3
docker service ls
docker service rm <NAME>
```

Swarm commands:
```bash
docker node ls
docker swarm join-token [manager|worker]
# Update a node to a manager:
docker node update --role manager <HOSTNAME>
# Change a node to a worker:
docker node update --role worker <HOSTNAME>
# Deploy a service to the swarm:
docker service create --replicas 3 alpine ping 1.1.1.1
# List services running on a node:
docker node ps <NODE>
# List nodes running a service;
docker service ps <SERVICE>
# Inspect service details:
docker service inspect --pretty <SERVICE>
```

## Service logs

Only works for logs that are not shipped to another service (via --log-driver). Reference: <https://docs.docker.com/engine/reference/commandline/service_logs/>

```bash
# Return all logs for a service:
docker service logs <SERVICE>
# Return all logs for a task:
docker service logs <TASK ID>
# Return unformatted logs with no truncing:
docker service logs --raw --no-trunc <SERVICE/TASK>
# Return that last 50 logs and follow:
docker service logs --tail 50 --follow <SERVICE/TASK>
# To pipe service logs output to grep, use 2>%1
docker service logs --tail 500 <SERVICE> 2>&1 | grep "Some search text"
```

# Docker Compose

Compose is a tool for defining and running multi-container Docker applications: Compose file format reference: <https://docs.docker.com/compose/compose-file/>

Compose is configured using YML-formatted files. A multi-container service Compose file example:

```yml
version: "3"

services:
  pgdb:
    image: postgres
    environment:
      - POSTGRES_PASSWORD=pass
    volumes:
      - pgdb-data:/var/lib/postgresql/data
  drupal:
    image: drupal
    ports:
      - "8082:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-themes:/var/www/html/themes
    depends_on:
      - pgdb

volumes:
  pgdb-data:
  drupal-modules:
  drupal-profiles:
  drupal-themes:
```

Using Compose to build images: use the [**build**](https://docs.docker.com/compose/compose-file/#build) options.

## Swarm Stacks using Compose

Stacks are another abstraction in Docker Swarm. Stacks accept Compose files as their declarative definition for services, volumes and  networks. Stack commands:
```bash
docker stack ls
docker stack services <STACK>  # Lists the current services for a stack
docker stack ps <STACK>  # Shows the tasks (running or not) for a stack
```

# A basic Docker image recipe

Basic steps are as follows:

- Log onto a Docker manager server (e.g. KENS-MATE-001)
- Pull the git repo into a temporary location
- Ensure requirements are pinned
- Create/edit the Dockerfile as required
- Build the image (cd to root git repo directory and run docker image build -t DOCKER_REPO:TAG .)
- Locally test run the image (docker container run --publish LOCAL_SERVER_PORT:INTERNAL_APPLICATION_PORT --env DEBUG=True DOCKER_REPO:TAG)
- You can run docker container exec -it CONTAINER_ID bash to get into the running bash for the container
- Once successfully running locally, push the image to Docker Hub (docker image push DOCKER_REPO:TAG, push once without tag for latest, and once with tag for a tagged release, will need to do docker login to push), see below
- Log onto a Docker runtime server as root (e.g. KENS-DOCKER-001, AWS-DOCKER-001)
- The root home will have a composefiles folder connected to a local Gitea repo
- Create/edit the composefile for your app (you can put non-prod and prod environments in this file, using latest for non-prod or tagged release for prod)
- Deploy the stack using the composefile (e.g. docker stack deploy -c COMPOSEFILENAME.yml SERVICE_NAME)
- Check using docker SERVICE|CONTAINER|IMAGE> ls
- Once successfully running, commit and push the composefiles repo

Best practices for building Docker images:

- https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
- https://blog.docker.com/2019/07/intro-guide-to-dockerfile-best-practices/
- Production-ready images for Python: https://pythonspeed.com/docker/

# Using the GitHub Container Repository (ghcr.io)

There are a couple of steps required for a developer to use the GitHub Container Repository to upload build Docker images.

1. The developer needs to create a Personal Access Token ([instructions here](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)) and use that as the password to authenticate with the registry (i.e. `docker login ghcr.io -u <USERNAME>`).
2. The uploaded image needs to be linked to a repository ([instructions here](https://docs.github.com/en/packages/learn-github-packages/connecting-a-repository-to-a-package)), either manually in GitHub or using a LABEL in the Dockerfile.
3. Permission settings for Github packages need to be set IN ADDITION to permissions on the linked repository. Having linked a package to a repo and pushed an image, open the repo in GitHub, click the container package on the right-hand side, then click "Package settings" on the right hand side again. Set the required Inherited Access settings on this page.
