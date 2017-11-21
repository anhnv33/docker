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
- 