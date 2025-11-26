
# Docker Advanced

This repository contains concepts, instructions, and examples of some Docker advanced topics like Dockerfile, Multi-stage build, Docker Networking, Volumes etc.

---
## ✅ Dockerfile: Blueprint for Images
A Dockerfile is a text file that contains all the commands needed to assemble a Docker image. It reads instructions from top to bottom, with each instruction creating a read-only layer.
### Example Dockerfile Structure

``` bash
# Start from a base image (minimal, secure, and small base)
FROM node:18-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy package files first to maximize build cache efficiency
COPY package*.json ./

# Install dependencies (creates a separate layer)
RUN npm install

# Copy the rest of the application source code
COPY . .

# Expose a port (metadata, useful for documentation)
EXPOSE 8080

# Define the command to run when the container starts (exec form preferred)
CMD ["npm", "start"]
```
### Example Build Command:
``` bash
docker build -t my-app:latest .
```
![poc - docker version](./images/version.png)

## ✅ Dockerfile Instruction Keywordse
These instructions define the steps in building a Docker image layer by layer.
|Keyword|Functionality|Similarities/Differences|Example|
|:---:|:---:|:---:|:---:|
|FROM|Specifies the base image for the build. Must be the first non-comment instruction.|N/A|FROM alpine:latest|
|WORKDIR|Sets the working directory for subsequent instructions (RUN, CMD, COPY, etc.).|Similar to using cd in a shell.|WORKDIR /usr/src/app|
|COPY|Copies files or directories from the host's build context into the image filesystem.|Differs from ADD: COPY is generally preferred as it is more explicit; it only copies local files.|COPY ./src /app/src|
|ADD|Copies files, but can also extract compressed files (tar) and fetch remote URLs.|Differs from COPY: Use COPY unless you need tar extraction or remote URL support.|ADD http://example.com/file.txt /app/|
|RUN|Executes a command during the image build process. Used for installation, compilation, etc.|Creates a new layer. Best practice is to chain commands with && to reduce layers.|RUN apt-get update && apt-get install -y git|
|CMD|Provides defaults for executing an executing container. Only the last CMD is used.|Differs from ENTRYPOINT: CMD is easily overridden when running a container (e.g., docker run <image> <new-command>).|CMD ["node", "server.js"]|
|ENTRYPOINT|Configures a container to run as an executable. The CMD acts as arguments to the ENTRYPOINT.|Differs from CMD: ENTRYPOINT is not easily overridden and sets the primary process.|ENTRYPOINT ["/bin/bash"]|
|ENV|Sets environment variables during the image build and in the running container.|N/A|ENV DEBUG=true|
|EXPOSE|Informs Docker that the container listens on the specified network ports at runtime (metadata).|Differs from -p in docker run: EXPOSE doesn't publish the port; it's documentation.|EXPOSE 80/tcp|
|VOLUME|Creates a mount point and marks it as holding externally mounted data (a Docker Volume).|N/A|VOLUME /data/logs|

## ✅ Health Check (HEALTHCHECK)
The HEALTHCHECK instruction tells Docker how to test a running container to check if it is still working correctly. This is vital for load balancers and orchestrators.
### Example Dockerfile Health Check
``` bash
# Check every 5 minutes, timeout after 3 seconds, retry 3 times
HEALTHCHECK --interval=5m --timeout=3s --retries=3 \
  CMD curl --fail http://localhost:8080/health || exit 1
```
### Viewing Health Status
``` bash
docker inspect --format='{{json .State.Health}}' <container_id>
```
![poc - docker run](./images/run.png)

## ✅ Environment Variables (ENV)
The ENV instruction sets persistent environment variables.
### Example Dockerfile ENV
``` bash
ENV APP_VERSION=1.0.0
# Multi-variable setting is more efficient:
ENV DB_HOST=database
ENV DB_PORT=5432
```
### Running a Container with Overridden ENV
``` bash
docker run -e DB_HOST=production-db my-app:latest
```
![poc - docker ps](./images/ps.png)

## ✅ Docker Prune: Cleaning Up Resources
The docker system prune command is crucial for reclaiming disk space by removing unused Docker objects.
|Command|Description|
|:---:|:---:|
|docker system prune|Removes: stopped containers, all dangling images (untagged, no container referencing them), dangling build cache, and unused networks.|
|docker system prune -a|Removes everything above, PLUS: all unused images (not just dangling ones, but all images not associated with a container).|
|docker volume prune|Removes all unused volumes. (Volumes are not included in system prune by default to prevent data loss).|
|docker builder prune|Removes build cache exclusively.|
![poc - docker ps -a](./images/psa.png)

## ✅ Docker Save/Load and Export/Import
These commands are used for sharing images or container filesystems without relying on a registry.
|Command|Purpose|Target Resource|Description|Example Command|
|:---:|:---:|:---:|:---:|:---:|
|docker save|Creates a tar archive of an image (including all its history/layers).|Image|Preserves all image layers and metadata. Best for moving images between registries/hosts.|docker save -o myimage.tar myimage:latest|
|docker load|Loads an image from a tar archive created by docker save.|Image|Puts the image back into the local Docker image cache.|docker load -i myimage.tar|
|docker export|Creates a tar archive of a container's filesystem (no history/layers) - only the final state.|Container|Doesn't include image history or metadata. Used for simple filesystem backup/transfer.|docker export <container_id> > container_fs.tar|
|docker import|Creates an image from an exported container filesystem archive.|Image|Creates a new, single-layer image from the archive.|`cat container_fs.tar|
![poc - docker logs](./images/logs.png)








## ✅ Stop a Container

``` bash
docker stop <container-name>
```
![poc - docker stop](./images/stop.png)

## ✅ Start/Restart a Container

``` bash
docker start <container-name>
docker restart <container-name>
```
![poc - docker start/restart](./images/start.png)

## ✅ Exec Into a Running Container

``` bash
docker exec -it <container-name> bash
```
![poc - docker exec](./images/exec.png)

## ✅ Inspect Container (deep details)

``` bash
docker inspect <container-name>
```
![poc - docker inspect](./images/ins.png)

## ✅ Remove a Container

``` bash
docker rm nginx-server
```
![poc - docker rm](./images/rm.png)

*To remove a container it must be stopped first or pass -f flag to force remove a running container.*

## ✅ Batch Stop/Delete Containers

Stop all running containers:

``` bash
docker stop $(docker ps -aq)
```
Remove all containers:

``` bash
docker rm $(docker ps -aq)
```
## ✅ List All Downloaded Docker Images

``` bash
docker images
```
![poc - docker images](./images/img.png)

## ✅ Remove an Image

``` bash
docker rmi <docker-image-name>
```
![poc - docker rmi](./images/rmi.png)

## ✅ Batch Remove All Images

``` bash
docker rmi $(docker images -q)
```

##  ✅ Container Restart Policy

Docker supports different restart policies to automatically restart containers under certain conditions.

```bash
docker run -d --restart=<policy> <docker-image-name>
```

### a. `no` (default)
Container does **not** restart automatically.

### b. `always`
Container restarts automatically whenever it stops.

### c. `on-failure`
Container restarts **only** if it exits with a non-zero status.

```bash
docker run -d --restart=on-failure:5 <docker-image-name>
```
*Restarts up to 5 times on failure.*

### d. `unless-stopped`
Container always restarts unless explicitly stopped by the user.

---

##  ✅ Disk Usage

### a. Check Docker disk usage

```bash
docker system df
```
![poc - docker disk info](./images/df.png)

*Shows overall space used by images, containers, and volumes.*

### b. Detailed Docker disk usage

```bash
docker system df -v
```
![poc - docker disk detail info](./images/dfv.png)

*Shows detailed image memory usage including layer sizes.*

---
