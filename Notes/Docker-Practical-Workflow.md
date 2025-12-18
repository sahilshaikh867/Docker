# 🐳 Docker Practical Workflow (From Zero to Production Thinking)

This section explains the **complete Docker workflow**, assuming you are **new to Docker** and want to learn it **practically**, step by step.  
It covers **why Docker is used**, **what happens internally**, and **what you actually do**, from installation to running real containers.

Think of this as a **start-to-end Docker journey**, the same way it happens in real DevOps work.

---

## 1. Why do we use Docker? (Before installing anything)

Before Docker, applications were installed directly on servers.  
Each server needed manual setup of:
- operating system packages
- runtime (Java, Node, Python)
- libraries
- configuration files

This caused problems:
- application worked on one machine but failed on another
- deployments were slow and risky
- rollback was difficult

Docker solves this by packaging the application and its environment together.  
When you use Docker, you are saying:

> “I want my application to run the same way everywhere.”

This is the foundation of modern DevOps and cloud systems.

---

## 2. Installing Docker (First practical step)

Docker must be installed on the system before containers can run.

### On Ubuntu
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
sudo chmod 777 /var/run/docker.sock
````

### On Amazon Linux

```bash
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
sudo chmod 777 /var/run/docker.sock
```

After installation, Docker Engine runs as a background service.
This service listens for Docker commands and manages images, containers, networks, and volumes.

---

## 3. Verifying Docker installation

```bash
docker --version
docker info
```

At this stage:

* Docker CLI sends commands
* Docker daemon executes them
* Docker engine is ready to work

---

## 4. Understanding what happens when Docker runs

When you use Docker:

* Docker pulls images from a registry (if not present)
* Images are stored locally
* Containers are created as isolated Linux processes
* Containers share the host kernel but have isolated filesystems

This is why Docker is fast and lightweight.

---

## 5. Pulling your first Docker image

An image is a **prebuilt environment**.

```bash
docker pull ubuntu
```

What happens internally:

* Docker contacts Docker Hub
* Downloads image layers
* Stores them locally
* Image becomes reusable

You are not running anything yet.
You are only **preparing the environment**.

---

## 6. Running your first container

```bash
docker run -it ubuntu
```

What happens:

* Docker creates a container from the image
* A new isolated filesystem is created
* A Linux process starts inside the container
* You get a shell inside the container

This container behaves like a mini Linux system, but it is not a VM.

---

## 7. Understanding container lifecycle

Containers have a lifecycle:

* create
* start
* run
* stop
* remove

If a container stops, it does not run again unless restarted.
If removed, its internal data is lost (unless volumes are used).

---

## 8. Running a real application (Nginx example)

```bash
docker pull nginx
docker run -itd --name web -p 80:80 nginx
```

What happens:

* Docker pulls the nginx image
* A container starts
* Port 80 inside the container is mapped to port 80 on the host
* You can access the app from a browser

This is your first real deployment.

---

## 9. Why Dockerfile is needed

Running containers manually is not scalable.
To automate application builds, Docker uses **Dockerfile**.

A Dockerfile defines:

* base image
* dependencies
* application files
* startup command

This makes builds repeatable and predictable.

---

## 10. Building your own Docker image

```bash
docker build -t myapp:v1 .
```

What happens:

* Docker reads the Dockerfile
* Executes instructions step by step
* Creates image layers
* Stores the image locally

The image is now a versioned artifact.

---

## 11. Running containers from your own image

```bash
docker run -itd --name mycontainer myapp:v1
```

Now:

* Your application runs inside a container
* Behavior is consistent
* Deployment is reproducible

---

## 12. Storing data using Docker volumes

Containers are temporary by nature.
Volumes are used to persist data.

```bash
docker volume create app-vol
docker run -itd --name c1 -v app-vol:/data ubuntu
```

Volumes live outside containers and survive restarts or deletions.

---

## 13. Container networking basics

Docker provides networking to allow containers to communicate.

```bash
docker network create mynet
docker run -itd --name c1 --network mynet ubuntu
docker run -itd --name c2 --network mynet ubuntu
```

Containers on the same network can communicate using container names.

---

## 14. Using Docker in real DevOps workflow

In real projects:

* Developers write code
* Dockerfile builds the image
* Image is tested
* Image is pushed to registry
* Same image is deployed to production

This eliminates environment mismatch.

---

## 15. What changes when you use Docker?

When you adopt Docker:

* manual server setup disappears
* deployments become predictable
* rollback becomes easy
* scaling becomes simple
* automation becomes natural

Docker turns deployment into a **controlled process**, not an emergency task.

---

## Final understanding

Docker is not just a tool.
It is a **workflow and discipline**.

If you understand this full flow—from installation to deployment—you are no longer a beginner.
You are thinking like a DevOps engineer.

---
