
**DOCKER COMPOSE – DEEP DIVE (but practical)**
---

# 🧠 WHY DOCKER COMPOSE EXISTS

Without Compose:

* `docker run` × 5 containers
* Ports, networks, volumes → manual pain
* Restart = headache

With Compose:

> **One YAML file → full application stack**

Think: **nginx + app + database** together.

---

## 🧩 CORE IDEA

`docker-compose.yml` describes:

* Services (containers)
* Networks
* Volumes

And Docker Compose runs them **together**.

---

# 📄 docker-compose.yml (ANATOMY)

### Minimal example

```yaml
version: "3.9"

services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

Run:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

---

## 🧱 SERVICES (HEART OF COMPOSE)

Each **service = one container**

### Example: Web + DB

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
```

👉 Compose auto-creates network
👉 Services talk via **service name** (`db`)

---

## 🌐 NETWORKING (AUTO MAGIC)

* One default bridge network
* Service name = DNS

Inside `web`:

```bash
ping db
```

No IPs. Clean. DevOps-approved 😎

---

## 💽 VOLUMES (DATA SURVIVES)

```yaml
services:
  db:
    image: mysql:8
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

Delete containers → data stays.

---

## ⚙️ BUILD WITH DOCKERFILE

```yaml
services:
  app:
    build: .
    ports:
      - "5000:5000"
```

Compose will:
1️⃣ Read Dockerfile
2️⃣ Build image
3️⃣ Run container

---

## 🔄 DEPENDS_ON (START ORDER)

```yaml
services:
  app:
    depends_on:
      - db
```

👉 Starts `db` first (not health-check, just order)

---

## 🔁 RESTART POLICIES (PROD TOUCH)

```yaml
restart: always
```

Options:

* `no`
* `always`
* `on-failure`
* `unless-stopped`

---

## 🧪 FULL PRACTICAL LAB (DO THIS)

### Project structure

```text
compose-lab/
├── docker-compose.yml
└── html/
    └── index.html
```

### index.html

```html
<h1>Hello from Docker Compose</h1>
```

### docker-compose.yml

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
```

Run:

```bash
docker compose up -d
```

Test:

```bash
curl localhost:8080
```

Change HTML → refresh browser → instant update 😎

---

## ⚠️ COMMON COMPOSE MISTAKES

❌ Wrong indentation (YAML strict)
❌ Forgetting quotes on ports
❌ Expecting `depends_on` to wait for DB ready

YAML = spaces matter. No tabs.

---

## 🧠 INTERVIEW GOLD Q&A

**Q:** Docker Compose vs Dockerfile?
**A:**
Dockerfile builds **one image**,
Compose runs **multiple containers together**.

---

## 🏁 DOCKER COMPOSE STATUS

You now know:

* Why Compose exists
* YAML structure
* Services, volumes, networks
* Real multi-container setup

This is **production-grade Docker skill**.

---
