# Docker and NVIDIA Container Toolkit Installation Guide

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
sudo curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) -o /etc/apt/keyrings/docker.asc
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


