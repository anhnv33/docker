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
- ```LABEL <key>=<value> <key>=<value> <key>=<value> ...```: The ```LABEL``` instruction adds metadata to an image. A ```LABEL``` is a key-value pair. To include spaces within a ```LABEL``` value, use quotes and backslashes as you would in command-line parsing
- ```MAINTAINER <name>```: The ```MAINTAINER``` instruction sets the Author field of the generated images.
- ```EXPOSE <port> [<port>/<protocol>...]```: The ```EXPOSE``` instruction informs Docker that the container listens on the specified network ports at runtime. You can specify whether the port listens on TCP or UDP, and the default is ```TCP``` if the protocol is not specified.
- ```ENV <key>=<value> ...```: The ```ENV``` instruction sets the environment variable ```<key>``` to the value ```<value>```.
- ```ADD``` has two forms:
    * ```ADD <src>... <dest>```
    * ```ADD ["<src>",... "<dest>"]``` (this form is required for paths containing whitespace)

The ```ADD``` instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the image at the path ```<dest>```.

Multiple  ```<src>``` resource may be specified but if they are files or directories then they must be relative to the source directory that is being built (the context of the build).

```sh
    ADD hom* /mydir/        # adds all files starting with "hom"
    ADD hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```

```sh
    ADD test relativeDir/          # adds "test" to `WORKDIR`/relativeDir/
    ADD test /absoluteDir/         # adds "test" to /absoluteDir/
```

When adding files or directories that contain special characters (such as [ and ]), you need to escape those paths following the ```Golang``` rules to prevent them from being treated as a matching pattern. For example, to add a file named arr[0].txt, use the following;
- Different between ```ADD``` and ```COPY```:
    * ```ADD``` allows ```<src>``` to be an URL
    * If the ```<src>``` parameter of ```ADD``` is an archive in a recognised compression format, it will be unpacked
- The table below shows what command is executed for different ENTRYPOINT / CMD combinations:

| 	|No ENTRYPOINT|	ENTRYPOINT exec_entry p1_entry|	ENTRYPOINT [“exec_entry”, “p1_entry”]|
|----|-----------|--------------------------------| -------------------------------------|
| No CMD| error, not allowed|	/bin/sh -c exec_entry p1_entry|	exec_entry p1_entry |
|CMD [“exec_cmd”, “p1_cmd”]|	exec_cmd p1_cmd |	/bin/sh -c exec_entry p1_entry|	exec_entry p1_entry exec_cmd p1_cmd|
|CMD [“p1_cmd”, “p2_cmd”]|	p1_cmd p2_cmd|	/bin/sh -c exec_entry p1_entry|	exec_entry p1_entry p1_cmd p2_cmd|
|CMD exec_cmd p1_cmd|	/bin/sh -c exec_cmd p1_cmd|	/bin/sh -c exec_entry p1_entry|	exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd|
- ```VOLUME ["/data"]```: The ```VOLUME``` instruction creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers
```sh
    FROM ubuntu
    RUN mkdir /myvol
    RUN echo "hello world" > /myvol/greeting
    VOLUME /myvol                           
````
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