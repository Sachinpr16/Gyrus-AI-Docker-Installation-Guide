# Gyrus AI Docker Installation Guide

This guide provides step-by-step instructions for installing Docker and configuring it to use NVIDIA GPUs on an Ubuntu-based system. This setup is essential for running GPU-accelerated applications in a containerized environment.

## Prerequisites

-   An Ubuntu-based Linux distribution.
-   An NVIDIA GPU with the appropriate proprietary drivers already installed.
-   `sudo` privileges.

---

## Step 1: Uninstall Old Docker Versions (Cleanup)

To prevent conflicts, it's best to start by removing any old or unofficial Docker packages from your system.

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do 
  sudo apt-get remove -y $pkg
done
```

## Step 2: Install Docker Engine
These commands will set up Docker's official apt repository and install the Docker Engine.

### 2.1. Set Up the Repository

Update the apt package index and install packages to allow apt to use a repository over HTTPS
```bash
sudo apt-get update
```
```
sudo apt-get install -y ca-certificates curl
```
```
sudo install -m 0755 -d /etc/apt/keyrings
```

Add Docker’s official GPG key
```
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```
```
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
Set up the repository
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
### 2.2 Install Docker Packages
Update the apt package index again
```
sudo apt-get update
```

Install Docker Engine, CLI, containerd, and plugins
```
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## Step 3: Install NVIDIA Container Toolkit
This toolkit allows Docker containers to access the host's NVIDIA GPU hardware.

### 3.1. Set Up the NVIDIA Repository

Add the NVIDIA package repository and GPG key

```
curl -fsSL [https://nvidia.github.io/libnvidia-container/gpgkey](https://nvidia.github.io/libnvidia-container/gpgkey) | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L [https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list](https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list) | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
Update the package list
```
sudo apt-get update
```

### 3.2. Install the Toolkit and Configure Docker
Install the NVIDIA container toolkit
```
sudo apt-get install -y nvidia-container-toolkit
```
Configure the Docker daemon to recognize the NVIDIA runtime
```
sudo nvidia-ctk runtime configure --runtime=docker
```

Restart the Docker service to apply the changes
```
sudo systemctl restart docker
```

# Step 4: Post-Installation Steps (Permissions)
Configure Docker to run without sudo. This is a recommended security practice that avoids running Docker commands as the root user.

Create the 'docker' group (may already exist)
```
sudo groupadd docker
```
Add your user to the 'docker' group
```
sudo usermod -aG docker $USER
```
Apply the new group membership to the current session
Note: For this change to take full effect, you may need to log out and log back in.
```
newgrp docker
```
# Step 5: Load Docker Image from Archive (One-time Step)
We provide a docker image archive as .tar file. You must extract it into a docker image.
```
docker load -i videosearchapi.tar
```

# Step 6: Running a GPU-Accelerated Container
Here is an example command to run Gyrus AI Docker container with access to all host GPUs.
```
docker run --gpus all --privileged -p 5800:5800  -v /path/to/mounting_dir:/work_dir name_of_container --batch_size 64 --working_dir /work_dir
```
## Command Breakdown

* --gpus all: Grants the container access to all available NVIDIA GPUs on the host.
* --privileged: Gives the container extended root capabilities. Use with caution.
* -p 5800:5800: Maps port 5800 on the host to port 5800 inside the container.
* -v $(pwd)/mountingdir:/work_dir: Mounts the mounting directory from the current host directory into the /work_dir directory inside the container. This is useful for persistent data.
* name_of_container: The name of the Docker image to run.
* --batch_size 64
* --working_dir /work_dir: 

These are arguments passed to the application running inside the container.



