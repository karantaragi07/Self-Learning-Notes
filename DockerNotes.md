# 🐳 Docker Notes

A complete set of notes covering Docker architecture, workflow, commands, and interview-ready points.

---

## 📑 Table of Contents
- [Docker Architecture](#-docker-architecture)
  - [Runtime](#1-runtime)
  - [Engine](#2-engine)
  - [Orchestration](#3-orchestration)
  - [Shim](#4-shim)
- [Docker Workflow](#-docker-workflow)
- [Registry](#-registry)
- [Containers](#-containers)
- [Common Docker Commands](#-common-docker-commands)
  - [Viewing Containers & Images](#-viewing-containers--images)
  - [Removing Containers & Images](#-removing-containers--images)
  - [Running Containers](#-running-containers)
  - [Logs & Inspect](#-logs--inspect)
  - [Build & Commit](#-build--commit)
- [Docker Image Layers (Interview Summary)](#-docker-image-layers-interview-summary)

---

## 📌 Docker Architecture

### 1. Runtime
The **runtime** in Docker is responsible for starting and stopping containers. It has two levels:

- **RunC (Low-level runtime)**  
  - Works directly with containers.  
  - Responsible for starting and stopping containers.  

- **Containerd (Higher-level runtime)**  
  - Manages **RunC**.  
  - Handles container lifecycle (create, start, stop, delete).  
  - Helps containers interact with the network (e.g., fetching data from the internet).  

---

### 2. Engine
The **Docker Engine** is the main component that allows interaction with Docker.

**Flow of execution:**

Docker Client ---> Docker Daemon (dockerd) ---> containerd ---> shim ---> runc ---> container


- **Docker Client (CLI):** Interface we use to interact with Docker.  
- **Docker Daemon (`dockerd`):** Background process that listens to Docker API requests and manages all Docker objects (containers, images, volumes, networks).  
- **Containerd:** Delegates work to the low-level runtime.  
- **Shim:** Keeps containers running independently of the Docker daemon.  
- **RunC:** The actual runtime that creates and manages containers.  

---

### 3. Orchestration
- Orchestration helps in **managing multiple containers**.  
- Handles **deployment, scaling, and load balancing**.  
- Examples: **Docker Swarm** and **Kubernetes**.  

---

### 4. Shim
- Enables **daemonless containers**.  
- Even if the Docker daemon goes down, containers continue running.  

---

## 📦 Docker Workflow
Dockerfile ---> Docker Image ---> Docker Container


- **Dockerfile:** A set of instructions to build an image.  
- **Docker Image:** Immutable, layered snapshot created from a Dockerfile.  
- **Docker Container:** A running instance of a Docker image.  

---

## 📤 Registry
- A **registry** is an online repository for Docker images.  
- Example: **Docker Hub**.  

---

## 🏗️ Containers
- Containers are **isolated environments** in which applications run.  
- Provide **consistency, portability, and isolation** across environments.  

---

## ⚡ Common Docker Commands

### 🔍 Viewing Containers & Images
- `docker container ps` → Show running containers.  
- `docker ps -a` → Show running, exited, and created containers.  
- `docker images` → List all images stored locally.  
- `docker images -q` → Show only image IDs.  

### 🗑️ Removing Containers & Images
- `docker rm <container_id>` → Remove a container.  
- `docker container prune -f` → Remove all stopped containers.  
- `docker image prune` → Remove dangling images.  
- `docker image prune -a` → Remove all unused images.  
- `docker rmi $(docker images -q)` → Remove all images from the system.  

### 🏃 Running Containers
- `docker run -d <image_name>` → Run a container in detached (background) mode.  
- `docker run -it <image_name>` → Start a new container interactively (e.g., Ubuntu shell).  
- `docker exec -it ubuntu bash` → Run a command inside a running container.  
- `docker run -d -p 8080:80 nginx` → Run Nginx with port mapping.  
  - **8080** → Host port.  
  - **80** → Container port.  
  - Data is forwarded from host port `8080` to container port `80`.  

### 📜 Logs & Inspect
- `docker logs <container_id>` → View logs of a running or stopped container.  
- `docker logs --since TIME <container_id>` → View logs from a specific time.  
- `docker inspect <image_name>` → Get low-level details about Docker objects (containers, images, volumes, networks).  

### 🛠️ Build & Commit
- `docker build -t <image_name> <dir_path>` → Build an image from a Dockerfile.  
- `docker commit -m "message" <container_id> <image_name>:<version>` → Save changes from a container into a new image.  

**Example:**
docker commit -m "added docker.txt file" 0h1t89 name_ubuntu:0.1


---

## 🧩 Docker Image Layers (Interview Summary)

- Docker images consist of **multiple layers**, each layer representing filesystem changes (like added files or installed software).  
- Layers are:  
  - **Immutable**  
  - **Read-only**  

- Shared layers are stored **globally** on the system:  
  - If multiple images use the same base layer, it is stored only once.  
  - Saves **disk space** and improves efficiency.  

- Benefits of layering:  
  - **Efficient builds** (only changed layers are rebuilt).  
  - **Caching** for faster builds.  
  - **Optimized downloads** (only new/changed layers are transferred).  

- At runtime, Docker combines these layers into a **unified filesystem**, which becomes the container’s root filesystem.  

⚡ **Key point:** Layers are a big reason why Docker is **lightweight and fast**.  

---

