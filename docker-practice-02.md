## ✅ **Current Progress Check**

| Step | Task                        | Status    | Notes                                                  |
| ---- | --------------------------- | --------- | ------------------------------------------------------ |
| 1️⃣  | Added Docker repo & GPG key | ✅ Done    | Correct repo for Ubuntu 24.04                          |
| 2️⃣  | Installed Docker components | ✅ Done    | Installed all — `docker-ce`, `cli`, `containerd`, etc. |
| 3️⃣  | Checked Docker service      | ✅ Running | Active since yesterday 💥                              |
| 4️⃣  | Ran `hello-world`           | ✅ Success | Docker working perfectly                               |
| 5️⃣  | Ran Ubuntu container        | ✅ Success | You even checked inside the container, nice!           |
| 6️⃣  | Checked running containers  | ✅         | You’ve got 2 Alpine containers running in background   |

---

## 🧩 **Next 3 Steps (Your Docker Level-Up Round)**

You’re now entering the *Docker workflow zone* — where you actually manage, build, and interact with containers like a pro.

---

### ⚙️ **Step 1: List Containers (Active + Stopped)**

To view **running containers**:

```bash
docker ps
```

To view **all containers (including stopped)**:

```bash
docker ps -a
```

🎯 *Goal:* See which containers are up, which are exited.
You’ll probably see your old `hello-world` and `ubuntu` containers here.

---

### 🧹 **Step 2: Stop & Remove Containers**

Let’s clean up your environment.

Stop all running containers:

```bash
docker stop $(docker ps -q)
```

Remove all containers:

```bash
docker rm $(docker ps -aq)
```

(If none are running, Docker will just skip those gracefully.)

🎯 *Goal:* Get a clean slate for image-building practice.

---

### 🧱 **Step 3: Create and Run Your Own Image**

Let’s make your *first custom Docker image* 👇

1️⃣ Create a folder:

```bash
mkdir docker-test && cd docker-test
```

2️⃣ Create a `Dockerfile`:

```bash
nano Dockerfile
```

Paste this inside:

```Dockerfile
# Base image
FROM ubuntu:latest

# Install some tools
RUN apt update && apt install -y curl

# Default command
CMD ["echo", "Yo! Dockerfile successfully built! 🚀"]
```

3️⃣ Build the image:

```bash
sudo docker build -t myfirstimage .
```

4️⃣ Run the image:

```bash
sudo docker run myfirstimage
```

If everything’s good — you’ll see the echo message printed out 🎉

🎯 *Goal:* Understand image build process & test your Dockerfile.

---

### ⚡ Optional Bonus:

To see all your images:

```bash
docker images
```

To remove an image (if you wanna rebuild):

```bash
docker rmi myfirstimage
```
