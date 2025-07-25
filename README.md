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
