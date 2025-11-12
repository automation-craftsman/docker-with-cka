
# Docker Introduction

Docker is a platform that simplifies building, shipping, and running applications using containers. A container is a lightweight, portable, and self-contained environment that includes everything an application needs to run—code, runtime, system tools, libraries, and settings. Unlike virtual machines, containers share the host system’s OS kernel, making them more efficient and faster to start. Docker allows developers to package applications consistently, run them anywhere (local, cloud, or server), and manage multiple containers easily. Essentially, Docker makes deployment predictable, scalable, and isolated without the overhead of full virtual machines.

---
## ✅ Installation Check

``` bash
docker version
```
or
``` bash
docker --version
```
![poc - docker version](./images/version.png)

## ✅ Pull Docker Image

``` bash
docker pull <docker-image-name>
```
![poc - docker pull](./images/pull.png)

## ✅ Create / Run a Docker Container

``` bash
docker run -d --name <container-name> -p <host-port>:<container-port> <docker-image-name>
```
![poc - docker run](./images/run.png)

## ✅ List Running Containers

``` bash
docker ps
```
![poc - docker ps](./images/ps.png)

## ✅ List All Containers (including stopped)

``` bash
docker ps -a
```
![poc - docker ps -a](./images/psa.png)

## ✅ View Logs

``` bash
docker logs <container-name>
```
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
