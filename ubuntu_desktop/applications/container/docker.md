# Install Docker Engine 

Quick notes about docker engine installation.

Read more at: https://docs.docker.com/engine/install/ubuntu/

# Uninstall all conflicting packages

> for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Set up Docker's apt repository

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

# Install the latest version

> sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add user to docker group

> sudo usermod -aG docker $LOGNAME

> newgrp docker

# To verify

> docker run hello-world

# To stop or remove containers

To list running containers:

> docker ps

To stop a container:

> docker stop container_id

To list stopped containers:

> docker ps -a --filter "status=exited"

To remove a container:

> docker rm container_id

# Remove all images and relase spaces

```
docker stop $(docker ps -q)
docker rmi -f $(docker images -q)
docker image prune -a
docker system prune -a --volumes
```

# To uninstall

> sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras

> sudo rm -rf /var/lib/docker

> sudo rm -rf /var/lib/containerd
