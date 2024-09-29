# Docker

## How to Install Docker on Ubuntu 24.04

Update & upgrade Ubuntu Repo's

    sudo apt update && sudo apt upgrade -y

### Install Docker

Install all required dependency packages.

    sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

Add the Docker GPG key to your server's keyring.

    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

Add the latest Docker repository to your APT sources.

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

Update the server package index.

    sudo apt update

Install Docker.

    sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

The above command installs the latest Docker version with the following plugins:

- `docker-ce`: The Docker engine community edition package.
- `docker-ce-cli`: Enables the Docker command line interface (CLI).
- `containerd.io`: A container runtime that monitors the lifecycle of Docker containers.
- `docker-buildx-plugin`: Improves Docker's image-building capabilities for multi-platform builds.
- `docker-compose-plugin`: Enables the management of multi-container Docker applications using YAML files.

View the installed Docker version on your server.

    sudo docker --version

### Manage the Docker System Service

Enable the Docker system service to start automatically at boot time.

    sudo systemctl enable docker

Start the Docker service.

    sudo systemctl start docker

View the Docker service status and verify that it's running.

    sudo systemctl status docker

Run the following command to stop Docker.

    sudo systemctl stop docker

Restart the Docker service.

    sudo systemctl restart docker

### Work with Docker

View all Docker images on the server.

    sudo docker images

List containers on the server and verify that the new container is up.

```bash
# all running containers
sudo docker ps

# all containers
sudo docker ps -a
```

Остановить и удалить `все` докер контейнеры

Чтобы удалить контейнеры, сначала их нужно остановить. Первая команда остановит запущенные контейнеры, если они есть. А вторая команда — удалит их.

    docker stop $(docker ps -a -q)

    docker rm $(docker ps -a -q)



Remove docker image

    docker rmi <имя-образа>

## More info

[How to Install Docker on Ubuntu 24.04](https://docs.vultr.com/how-to-install-docker-on-ubuntu-24-04)

[Selectel: Что такое Docker: для чего он нужен и где используется](https://selectel.ru/blog/what-is-docker/)

[Selectel: Установка и настройка Docker в Ubuntu](https://selectel.ru/blog/docker-install-ubuntu/)