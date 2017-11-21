# Docker Introduction
Created by AnhNV Created Date: 19/10/2017

This document provides informaion about the following:

- Introduction of Docker

**Reference**:


## 1. Introduction
- Docker describes themselves as "an open platform for developers and sysadmins to build, ship, and run distributed applications".
- Docker allows you to run containers
- A container is a sandboxed process running an application and its dependencies on the host operating system
- The application inside the container considers itself to be the only process running on the machine while the machine can run multiple containers independently.
- With Docker, all containers are started based on a Docker image
- These images contain everything required to launch the process;

## 2. Docker command
- Find an image for Redis: ```docker search redis ```
- By default, Docker will run a command in the foreground. To run in the background, the option -d needs to be specified: ```docker run -d redis```
- Command lists all running containers, the image used to start the container and uptime: ```docker ps```
- Find out information about individual containers: ```docker inspect <friendly-name|container-id>```
- Display messages the container has written to standard error or standard out: ```docker logs <friendly-name|container-id>```
- If a service needs to be accessible by a process not running in a container, then the port needs to be exposed via the Host
- Using ```-p <host-port>:<container-port>``` option to expose port, example: ```docker run -d --name redisHostPort -p 6379:6379 redis:latest```
- Expose Redis but on a randomly available port: ```docker run -d --name redisDynamic -p 6379 redis:latest```
- Command line discovered expose port: ```docker port redisDynamic 6379```
- Persisting data, command line: ```docker run -d --name redisMapped -v <host-dir>:<container-dir> redis```
- Run container foreground: ```docker run -it ubuntu bash```

## 2. Docker file
- ```FROM nginx:alpine```: defines our base images
- ```COPY . /usr/share/nginx/html```: copy the content of the directory into a particular location inside the container
- ```docker build -t <image-name>:<tag> <build-directory>```: docker build image, example ```docker build -t webserver-image:v1 .```
- ```WORKDIR <directory>```: ensure that all future commands are executed from the directory relative to our application
- ```RUN <command>```: run command inside container
- ```CMD ["npm", "start"]```: run command in background container