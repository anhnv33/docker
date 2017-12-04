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
- Remove all images: ```docker rmi $(docker images -f dangling=true -q)```

## 2. Docker file
- ```ARG CODE_VERSION=latest```: defines agruments used for docker file, an ```ARG``` declared before a ```FROM``` is outside of a build stage, so it can’t be used in any instruction after a ```FROM```. To use the default value of an ```ARG``` declared before the first ```FROM``` use an ```ARG``` instruction without a value inside of a build stage:
```sh
    ARG VERSION=latest
    FROM busybox:$VERSION
    ARG VERSION
    RUN echo $VERSION > image_version
```
- ```RUN``` has 2 forms:
    * ```RUN <command>``` (shell form, the command is run in a shell, which by default is ```/bin/sh -c``` on Linux or ```cmd /S /C``` on Windows)
    * ```RUN ["executable", "param1", "param2"]``` (exec form)
- The ```CMD``` instruction has three forms:
    * ```CMD ["executable","param1","param2"]``` (exec form, this is the preferred form)
    * ```CMD ["param1","param2"]``` (as default parameters to ENTRYPOINT)
    * ```CMD command param1 param2``` (shell form)

There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.
- ```FROM nginx:alpine```: defines our base images
- ```COPY . /usr/share/nginx/html```: copy the content of the directory into a particular location inside the container
- ```docker build -t <image-name>:<tag> <build-directory>```: docker build image, example ```docker build -t webserver-image:v1 .```
- ```WORKDIR <directory>```: ensure that all future commands are executed from the directory relative to our application
- ```RUN <command>```: run command inside container
- ```CMD ["npm", "start"]```: run command in background container
- ```docker run -d --name <container-name> -e NODE_ENV=production -p 3000:3000 my-nodejs-app```: define environment value when run container
- ```ONBUILD ...```: The application specific commands won't be executed util the built image is used as a base image
- ```.dockerignore```: file would be stored in source control, ignore file when build image
- Create a Data Container for storing configuration files using: ```docker create -v /config --name dataContainer busybox```
- The following command will copy the config.conf file into our dataContainer and the directory config: ```docker cp config.conf dataContainer:/config/```
- Using the --volumes-from <container> option we can use the mount volumes from other containers inside the container being launched: ```docker run --volumes-from dataContainer ubuntu ls /config```
- Export / Import Data Container: ```docker export <containerName> > <file.tar>```, ```docker import <file.tar>```