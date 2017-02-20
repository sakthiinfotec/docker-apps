# Dockerizing apps

Docker is a containerization technology that enables us to cleanly abstract an environment configuration to a file (or set of files), and run it in a protected, isolated environment on a host. Docker allows us to package an application with all of its dependencies into a standardized unit, called a container, for software development. A container is a stripped-to-basics version of a Linux operating system.

Frequently used Docker commands,
```
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

# Docker Hub login/logout
docker login
docker logout
```
