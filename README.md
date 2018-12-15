# Dockerizing apps

Docker is a containerization technology that enables us to cleanly abstract an environment configuration to a file (or set of files), and run it in a protected, isolated environment on a host. Docker allows us to package an application with all of its dependencies into a standardized unit, called a container, for software development. A container is a stripped-to-basics version of a Linux operating system.

Frequently used Docker commands,
```sh
docker version
docker info
docker images

docker-machine --version
docker-compose --version

# Run application
docker run <app-name>
docker run -p <localhost-os-port>:<container-port> -d <username>/<app-name>

# Process Status
docker ps

# Print log
docker logs <container id>

docker build -t <username>/<app-name> .
docker exec -it <container id> /bin/bash

# Docker Hub(Image repository) login/logout
docker login
docker logout

# Search for images
docker search <image-name>
docker search redis
docker search nginx

## Pull images from Hub
# by default latest version
docker pull <image-name>
docker pull redis 

# specific version
docker pull python:2.7 

# multiple or all the versions
docker pull -a python

# Tag and push our custom image to Docker Hub
docker tag <local-docker-image-id> <docker-hub-user>/<repository-name>[:<tag>]
docker tag f639d0007050 sakthiinfotec/docker-node-web-app:2.5

docker commit <local-container-id> <docker-hub-user>/<repository-name>[:<tag>]
docker commit b1eb3e5363fc sakthiinfotec/docker-node-web-app:latest

# Remove unused containers and images
docker rm <container-id>
docker rm 66ed57605ae6

docker image remove <image-id>
docker image remove 6fae60ef3446
```

#### Attach Host's shared directory path with container
In Windows `docker run --rm -it -v C:\Users\docker\data:/data alpine`  
In Linux `docker run --rm -it -v /home/docker/data:/data alpine`

#### Container Linking - Interconnecting or communicating between two container services
Linking a container simply extracts the runtime information(such as IP address, exposed ports) of the dependent container service(Eg. MongoDB) and exposes that information into depending container(Eg. Node.js - Web API service).
```sh
# Pulling the latest MongoDB and Node.js official images from the docker hub
docker pull mongo
docker pull node

# Connecting the MongoDB container to the Node.js container using docker's "container linking" method
docker run -d --name MongoDB mongo
docker run --link=MongoDB:mongodb -it node /bin/bash
```
Below is the runtime information of linked "MongoDB" container from a "Node.js" container

```sh
root@a88d12df0fb7:/# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      mongodb 50ba37308b92 MongoDB
172.17.0.3      a88d12df0fb7

root@a88d12df0fb7:/# env | grep MONGODB
MONGODB_ENV_MONGO_PACKAGE=mongodb-org
MONGODB_NAME=/clever_curran/mongodb
MONGODB_PORT_27017_TCP=tcp://172.17.0.2:27017
MONGODB_PORT=tcp://172.17.0.2:27017
MONGODB_ENV_MONGO_VERSION=3.4.1
MONGODB_ENV_GOSU_VERSION=1.7
MONGODB_PORT_27017_TCP_PORT=27017
MONGODB_ENV_MONGO_MAJOR=3.4
MONGODB_PORT_27017_TCP_PROTO=tcp
MONGODB_PORT_27017_TCP_ADDR=172.17.0.2
```

From the above listed runtime environment variables, we are interested in only two entries such as IP(`MONGODB_PORT_27017_TCP_ADDR=172.17.0.2`) and PORT(`MONGODB_PORT_27017_TCP_PORT=27017`) to connect MongoDB from depending application.

#### Linking or accessing from Node.js container service
Access an environment details using `process.env` reference, where `process` is a global object provided by Node.js to access current process information.
```javascript
const MONGODB_HOST = process.env.MONGODB_PORT_27017_TCP_ADDR;
const MONGODB_PORT = process.env.MONGODB_PORT_27017_TCP_PORT;
```

#### Linking or accessing from Java container service
```java
private static final String MONGODB_HOST = System.getenv("MONGODB_PORT_27017_TCP_ADDR");
private static final String MONGODB_PORT = System.getenv("MONGODB_PORT_27017_TCP_PORT");
```
