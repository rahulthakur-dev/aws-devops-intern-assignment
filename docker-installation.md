### Docker Engine Installation (Ubuntu, apt repository method)

#### Prerequisites

Update the package index before adding any new repository.
```
sudo apt update
```

Install packages required to add a repository over HTTPS.
```
sudo apt install ca-certificates curl
```

#### Set Up Docker's Apt Repository

Create the keyrings directory used to store the GPG key.
```
sudo install -m 0755 -d /etc/apt/keyrings
```

Download Docker's official GPG key.
```
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

Make the key file readable by all users.
```
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add the Docker repository definition to apt sources.
```
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Refresh the package index so the new repository is picked up.
```
sudo apt update
```

#### Install Docker Packages

Install Docker Engine, CLI, containerd, Buildx, and Compose plugins.
```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### Verify the Installation

Check whether the Docker service is active.
```
sudo systemctl status docker
```

Start the Docker service manually if it is not running.
```
sudo systemctl start docker
```

Enable Docker to start automatically on boot.
```
sudo systemctl enable docker
```

Run the hello-world test image to confirm Docker is working.
```
sudo docker run hello-world
```

A successful run downloads the `hello-world` image, starts a container, prints a confirmation message, then exits.

#### Troubleshooting

##### Permission denied while trying to connect to the Docker daemon socket

Cause: current user is not in the `docker` group, so `docker` commands require `sudo`.
```
sudo usermod -aG docker $USER
newgrp docker
```

##### Docker service inactive after install

Check the service status and logs.
```
sudo systemctl status docker
sudo journalctl -u docker.service --no-pager -n 50
```

##### GPG key or repository fetch fails

Verify network access to Docker's servers and re-check the key file.
```
curl -Is https://download.docker.com/linux/ubuntu/gpg
ls -l /etc/apt/keyrings/docker.asc
```

##### `apt install` reports unable to locate package

Cause: `docker.sources` file has an incorrect `Suites` value or `apt update` was not re-run after adding the repository.
```
cat /etc/apt/sources.list.d/docker.sources
sudo apt update
```
