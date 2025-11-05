### 🧩 Step 1: Prep your environment

Make sure Docker is installed and running:

```bash
sudo systemctl start docker
sudo systemctl enable docker
docker --version
```

If it gives an authentication error (`AUTHENTICATION FAILED`), I’ll help fix that — just tell me what it shows.

---

### 🏗 Step 2: Create a project folder

Let’s make a small app directory to work in:

```bash
mkdir my-docker-app
cd my-docker-app
```

---

### 💻 Step 3: Create a simple app

Let’s go with a **Python Flask app** (super lightweight).

Create a file named `app.py`:

```bash
nano app.py
```

Paste this:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "🐳 Hello from your Dockerized App!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

Save and exit.

---

### 📦 Step 4: Create `requirements.txt`

Create one more file:

```bash
nano requirements.txt
```

Add:

```
flask
```

---

### 🐋 Step 5: Create a `Dockerfile`

Now let’s containerize the app:

```bash
nano Dockerfile
```

Paste:

```Dockerfile
# Use official Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Install dependencies
RUN pip install -r requirements.txt

# Expose port
EXPOSE 5000

# Run app
CMD ["python", "app.py"]
```

Save and exit.

---

### ⚙️ Step 6: Build your Docker image

Run:

```bash
sudo docker build -t my-docker-app .
```

This will download Python, install Flask, and create an image.

---

### 🚀 Step 7: Run your container

Run it:

```bash
sudo docker run -d -p 5000:5000 my-docker-app
```

Check containers:

```bash
sudo docker ps
```

---

### 🌍 Step 8: Test the app

Open your browser or use `curl`:

```bash
curl http://localhost:5000
```

You should see:

```
🐳 Hello from your Dockerized App!
```

---

### 🧹 Step 9: Stop & remove container (optional)

To stop:

```bash
sudo docker stop <container_id>
```

To remove:

```bash
sudo docker rm <container_id>
```
