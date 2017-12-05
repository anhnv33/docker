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
- ```docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]```: Create a new image from a container’s changes 

    * Commit a container:
    ```sh
        $ docker ps
    
        CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES
        c3f279d17e0a        ubuntu:12.04        /bin/bash           7 days ago          Up 25 hours                            desperate_dubinsky
        197387f1b436        ubuntu:12.04        /bin/bash           7 days ago          Up 25 hours                            focused_hamilton
        
        $ docker commit c3f279d17e0a  svendowideit/testimage:version3
        
        f5283438590d
        
        $ docker images
        
        REPOSITORY                        TAG                 ID                  CREATED             SIZE
        svendowideit/testimage            version3            f5283438590d        16 seconds ago      335.7 MB
    ```
    * Commit a container with new configurations
    ```sh
        $ docker ps

        CONTAINER ID       IMAGE               COMMAND             CREATED             STATUS              PORTS              NAMES
        c3f279d17e0a        ubuntu:12.04        /bin/bash           7 days ago          Up 25 hours                            desperate_dubinsky
        197387f1b436        ubuntu:12.04        /bin/bash           7 days ago          Up 25 hours                            focused_hamilton
        
        $ docker inspect -f "{{ .Config.Env }}" c3f279d17e0a
        
        [HOME=/ PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin]
        
        $ docker commit --change "ENV DEBUG true" c3f279d17e0a  svendowideit/testimage:version3
        
        f5283438590d
        
        $ docker inspect -f "{{ .Config.Env }}" f5283438590d
        
        [HOME=/ PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin DEBUG=true]   
    ```
- ```docker container attach [OPTIONS] CONTAINER```: Attach local standard input, output, and error streams to a running container
- ```docker container commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]```: Create a new image from a container’s changes
- ```docker container cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH| - docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH```: Copy files/folders between a container and the local filesystem
- ```docker container create [OPTIONS] IMAGE [COMMAND] [ARG...] - docker create [OPTIONS] IMAGE [COMMAND] [ARG...]```: Create a new container
- ```docker container diff CONTAINER```: Inspect changes to files or directories on a container’s filesystem
- ```docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]```: Run a command in a running container
- ```docker container export [OPTIONS] CONTAINER```: Export a container’s filesystem as a tar archive
    > **Options**: --output , -o		Write to a file, instead of STDOUT
- ```docker container kill [OPTIONS] CONTAINER [CONTAINER...]```: Kill one or more running containers
- ```docker container logs [OPTIONS] CONTAINER```: Fetch the logs of a container
- ```docker container ls [OPTIONS]```: List containers
- ```docker container pause CONTAINER [CONTAINER...]```: Pause all processes within one or more containers
- ````docker container port CONTAINER [PRIVATE_PORT[/PROTO]]```: List port mappings or a specific mapping for the container
- ```docker container prune [OPTIONS]```: Remove all stopped containers
- ```docker container rename CONTAINER NEW_NAME```: Rename a container
- ```docker container restart [OPTIONS] CONTAINER [CONTAINER...]```: Restart one or more containers
- ```docker container rm [OPTIONS] CONTAINER [CONTAINER...]```: Remove one or more containers
- ```docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]```: Run a command in a new container
- ```docker container start [OPTIONS] CONTAINER [CONTAINER...]```: Start one or more stopped containers
- ```docker container stats [OPTIONS] [CONTAINER...]```: Display a live stream of container(s) resource usage statistics
- ```docker container stop [OPTIONS] CONTAINER [CONTAINER...]```: Stop one or more running containers
- ```docker container top CONTAINER [ps OPTIONS]```: Display the running processes of a container
- ```docker container unpause CONTAINER [CONTAINER...]```: Unpause all processes within one or more containers
- ```docker container update [OPTIONS] CONTAINER [CONTAINER...]```: Update configuration of one or more containers
- ```docker container wait CONTAINER [CONTAINER...]```: Block until one or more containers stop, then print their exit codes
- ```docker exec [OPTIONS] CONTAINER COMMAND [ARG...]```: Run a command in a running container
- ```docker export [OPTIONS] CONTAINER```: Export a container’s filesystem as a tar archive
## 2. Docker file
- ```ARG CODE_VERSION=latest```: defines agruments used for docker file, an ```ARG``` declared before a ```FROM``` is outside of a build stage, so it can’t be used in any instruction after a ```FROM```. To use the default value of an ```ARG``` declared before the first ```FROM``` use an ```ARG``` instruction without a value inside of a build stage:
```sh
    ARG VERSION=latest
    FROM busybox:$VERSION
    ARG VERSION
    RUN echo $VERSION > image_version
```
> **Warning:** It is not recommended to use build-time variables for passing secrets like github keys, user credentials etc. Build-time variable values are visible to any user of the image with the ```docker history``` command.

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

This Dockerfile results in an image that causes docker run, to create a new mount point at ```/myvol``` and copy the greeting file into the newly created volume.
- ```USER <user>[:<group>] or USER <UID>[:<GID>]```: The ```USER``` instruction sets the user name (or UID) and optionally the user group (or GID) to use when running the image and for any ```RUN```, ```CMD``` and ```ENTRYPOINT``` instructions that follow it in the ```Dockerfile```.
> Warning: When the user does doesn’t have a primary group then the image (or the next instructions) will be run with the root group.

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