# Docker

## Overview

This content is intended as a summary introduction for developers that may need to work with and support Docker-based applications in production, by guiding users to other information resources and suggesting a sensible order in which to learn things.

## External learning references

In addition to this page, the following links may contain useful or more-detailed content for learning Docker:

- <https://docs.docker.com/get-started/docker-overview/>
- <https://docker-curriculum.com>
- <https://docs.linuxserver.io/general/containers-101>

## What is Docker, and what is a Container?

[Docker](https://opensource.com/resources/what-docker) is a open source computer program that performs operating-system-level virtualisation (also known as containerisation). Docker allows us to deploy and run software applications inside **Containers**, which (roughly) are applications that are packaged up with all the assets needed to run (binaries, libraries, static files, etc.) on any suitable host that runs the Docker daemon. A Container is (approximately) an executable which is a kind of extremely lightweight VM without including any OS of its own. In effect, Docker provides a set of APIs that a Container can use to access the underlying OS functions.

References:

- <https://docs.docker.com/get-started/docker-overview/>
- <https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/>

## What is an Image?

A **Docker image** is a read‑only, versioned package that contains an application and everything it needs to run—code, runtime, libraries, environment variables, and configuration.

Images are stored in a **Registry** (see below), and are (roughly) equivalent to a tagged Git commit of a Container. Images are built from instructions in a **Dockerfile** (see below).

References:

- <https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/>
- <https://www.geeksforgeeks.org/devops/what-is-docker-image/>

## What is a Dockerfile?

A Dockerfile is a text document that contains all of the commands a user would call on the command line to assemble an Image. An analogy: a Dockerfile is like a cake recipe, an Image is a pre-made cake mix, a Container is a finished cake.

References:

- <https://docs.docker.com/get-started/docker-concepts/building-images/writing-a-dockerfile/>
- <https://docs.docker.com/engine/reference/builder/>

## What is a Registry?

A Docker registry is a storage and content delivery system for named Docker images. It can be self-hosted, third-party-hosted, or the official Docker Hub registry (<https://hub.docker.com/>).

References:

- <https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-registry/>

# Docker command examples

## Container creation and usage

Pull an image from the online registry to local storage:

```bash
docker pull <IMAGE NAME>
```

Basic run command:

```bash
docker container run --publish 80:80 nginx
```

Breakdown:

- Searches Docker Hub for an image called "nginx" and downloads the latest one (caches it locally).
- Creates a new container based on the image.
- Gives it a virtual IP on a private network in the Docker engine.
- Open port 80 on the host, and forward that to port 80 inside the container.
- Runs the container in the foreground.

```bash
docker container run --publish 80:80 --detach nginx
```

Same as above, but detached. Stop a container:

```bash
docker container stop CONTAINER_ID
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
docker container logs NAME
docker container top NAME
docker container inspect NAME
docker container stop NAME
```

Remove containers:

```bash
docker container ls -a
docker container rm ID1 ID2
docker container rm -f ID
```

Manage containers assignment (command list):

```bash
docker container run --publish 8082:80 --detach --name webserver httpd
docker container logs webserver
docker container run --publish 3306:3306 -d -n mysql_db -e MYSQL_RANDOM_ROOT_PASSWORD=true
docker container run --publish 3306:3306 -d --name mysql_db -e MYSQL_RANDOM_ROOT_PASSWORD=true
docker container run --publish 3306:3306 --detach --name mysql_db -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
docker image ls
docker container ls -a
```

Viewing information about running containers:

```bash
docker container top NAME
docker container stats
```

Getting inside a running container:

```bash
# Start a container interactively and run sh in it:
docker container start -it alpine sh
# Run a bash shell on a running container:
docker container exec -it NAME /bin/bash
```

Misc. command dump:

```bash
# Display IP address of a running container:
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' NAME
# Remove all exited containers:
docker ps -a | grep Exit | cut -d ' ' -f 1 | xargs docker rm
```

Docker container networking: <https://docs.docker.com/network/>

```bash
docker network ls
docker network inspect NAME
docker network create NAME
docker network connect NETWORK CONTAINER
docker network disconnect NETWORK CONTAINER
```

## Container lifetime & persistent data

- Containers are usually immutable and ephemeral.
- Non-ephemeral data (databases, uploaded files, etc.) needs to be persisted. It is more important than the container itself.
- Data can be persisted using volumes and bind mounts.

Define a named volume when starting a container:

```bash
docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
docker container run -d --name pgdb -e POSTGRES_PASSWORD=pass -p 5432:5432 -v pgdb:/var/lib/postgresql/data postgres
```

Bind mounting maps a host file/dir to a container file/dir. Can't use this in a Dockerfile, must be at container run. E.g.:

```bash
docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v /path/on/host:/path/in/container mysql
```

## A basic Docker image recipe

Documentation: <https://docs.docker.com/build/>

Basic steps are as follows:

- Log onto a Docker manager server
- Pull the git repo into a temporary location
- Ensure requirements are pinned
- Create/edit the Dockerfile as required
- Build the image (cd to root git repo directory and run `docker image build -t DOCKER_REPO:TAG .`)
- Locally test run the image (`docker container run --publish LOCAL_SERVER_PORT:INTERNAL_APPLICATION_PORT --env DEBUG=True DOCKER_REPO:TAG`)
- You can run `docker container exec -it CONTAINER_ID /bin/bash` to get into the running bash for the container
- Once successfully running locally, push the image to Docker Hub (`docker image push DOCKER_REPO:TAG`, push once without tag for latest, and once with tag for a tagged release)

Best practices for building Docker images: <https://docs.docker.com/build/building/best-practices/>.

## Using the GitHub Container Repository (ghcr.io)

There are a couple of steps required for a developer to use the GitHub Container Repository to upload build Docker images.

1. The developer needs to create a Personal Access Token ([instructions here](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)) and use that as the password to authenticate with the registry (i.e. `docker login ghcr.io -u <USERNAME>`).
2. The uploaded image needs to be linked to a repository ([instructions here](https://docs.github.com/en/packages/learn-github-packages/connecting-a-repository-to-a-package)), either manually in GitHub or using a LABEL in the Dockerfile.
3. Permission settings for Github packages need to be set IN ADDITION to permissions on the linked repository. Having linked a package to a repo and pushed an image, open the repo in GitHub, click the container package on the right-hand side, then click "Package settings" on the right hand side again. Set the required Inherited Access settings on this page.
