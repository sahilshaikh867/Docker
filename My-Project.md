
---

## 🧾 **Project Name:**

**Flask Full-Stack Docker Environment (with MySQL & phpMyAdmin)**

---

## ⚙️ **What It Does:**

You’ve got a 3-container stack that looks like this:

| Container       | Purpose                    | Port |
| --------------- | -------------------------- | ---- |
| 🧱 `web`        | Flask app (Python backend) | 5000 |
| 🐬 `db`         | MySQL database             | 3306 |
| 🧰 `phpmyadmin` | GUI to manage MySQL        | 8080 |

---

## 🧩 **Folder Structure**

```
my-compose-app/
│
├── Dockerfile
├── docker-compose.yml
├── app.py
├── requirements.txt
└── templates/
    └── index.html
```

---

## 🐳 **Dockerfile (for Flask app)**

```dockerfile
# Base image
FROM python:3.9

# Set working directory
WORKDIR /app

# Copy dependency file
COPY requirements.txt .

# Install dependencies
RUN pip install -r requirements.txt

# Copy all files to container
COPY . .

# Expose port
EXPOSE 5000

# Run the app
CMD ["python", "app.py"]
```

---

## 🧾 **requirements.txt**

```
Flask
mysql-connector-python
```

---

## 🧠 **app.py**

```python
from flask import Flask, render_template
import mysql.connector

app = Flask(__name__)

@app.route('/')
def index():
    try:
        conn = mysql.connector.connect(
            host='db',
            user='root',
            password='root',
            database='testdb'
        )
        cursor = conn.cursor()
        cursor.execute("SHOW TABLES;")
        tables = cursor.fetchall()
        return render_template('index.html', tables=tables)
    except Exception as e:
        return f"Database connection failed: {e}"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```

---

## 🎨 **templates/index.html**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Flask + MySQL Docker Setup</title>
</head>
<body>
  <h1>🚀 Flask App Connected to MySQL!</h1>
  <h3>Available Tables:</h3>
  <ul>
    {% for t in tables %}
      <li>{{ t[0] }}</li>
    {% endfor %}
  </ul>
</body>
</html>
```

---

## 🐋 **docker-compose.yml**

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
    environment:
      DB_HOST: db
      DB_USER: root
      DB_PASSWORD: root
      DB_NAME: testdb
    networks:
      - mynet

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: testdb
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - mynet

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: always
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: root
    networks:
      - mynet

volumes:
  db_data:

networks:
  mynet:
```

---

## 🧠 Run the Project

```bash
sudo docker compose up -d --build
```

**Test URLs:**

* Flask → `http://<your-ec2-ip>:5000`
* phpMyAdmin → `http://<your-ec2-ip>:8080`

  * Server: `db`
  * Username: `root`
  * Password: `root`

---

## 🧹 Stop Everything

```bash
sudo docker compose down
```

If you also want to delete DB data:

```bash
sudo docker compose down -v
```

---

## 🚀 Bonus Upgrade Options

| Goal                      | Next Step                                           |
| ------------------------- | --------------------------------------------------- |
| Host your app publicly    | Deploy to **Docker Hub** or **Render**              |
| Add frontend (React/HTML) | Add `nginx` service for reverse proxy               |
| Make it cloud-ready       | Use **Kubernetes + HPA** (your next DevOps goal 😉) |
| Automate build            | Create a **GitHub Actions CI/CD pipeline**          |

---

## 🧩 Step 1: Stay in your project folder

If not already:

```bash
cd my-compose-app
```

---

## 🐋 Step 2: Update your `docker-compose.yml`

Open the file:

```bash
nano docker-compose.yml
```

Replace everything with this ⬇️

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
    environment:
      DB_HOST: db
      DB_USER: root
      DB_PASSWORD: root
      DB_NAME: testdb
    networks:
      - mynet

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: testdb
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - mynet

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: always
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: root
    networks:
      - mynet

volumes:
  db_data:

networks:
  mynet:
```

---

## 🧱 Step 3: Bring the stack up

Run:

```bash
sudo docker compose up -d --build
```

---

## 🧠 Step 4: Check containers

```bash
sudo docker compose ps
```

You should see:

```
web          ... Up ... 0.0.0.0:5000->5000/tcp
db           ... Up ... 0.0.0.0:3306->3306/tcp
phpmyadmin   ... Up ... 0.0.0.0:8080->80/tcp
```

---

## 🌍 Step 5: Test everything

**Flask App:**

```bash
curl http://localhost:5000
```

or in browser:

```
http://<your-public-ip>:5000
```

**phpMyAdmin:**

```
http://<your-public-ip>:8080
```

➡️ Login credentials:

* **Server:** `db`
* **Username:** `root`
* **Password:** `root`

---

## 💾 Step 6: Test persistence

1. Create a table in phpMyAdmin (or via CLI).
2. Then run:

   ```bash
   sudo docker compose down
   sudo docker compose up -d
   ```
3. Your data will still be there — because of this:

   ```yaml
   volumes:
     - db_data:/var/lib/mysql
   ```
---
Creation of tables using PHPadminpage
---

## 🧰 **Option 1 — Using phpMyAdmin (easiest)**

1. Open phpMyAdmin in your browser:

   ```
   http://<your-public-ip>:8080
   ```

2. Login:

   * **Server:** `db`
   * **Username:** `root`
   * **Password:** `root`

3. On the left sidebar, click on your database — `testdb`.

4. Click **SQL** tab or **Create table** button.

5. Create a table named `users` with these columns:

   | Column Name | Type    | Length | Key | Extra          |
   | ----------- | ------- | ------ | --- | -------------- |
   | id          | INT     | 11     | PRI | AUTO_INCREMENT |
   | name        | VARCHAR | 100    |     |                |
   | email       | VARCHAR | 100    |     |                |

6. Hit **Save** ✅

7. Then insert sample data (phpMyAdmin → Insert tab):

   | name   | email                                           |
   | ------ | ----------------------------------------------- |
   | sahil | [sahil@example.com](mailto:sahil@example.com)    |

8. Done 🎉 — table created and data inserted.

---

## 🧑‍💻 **Option 2 — Using MySQL CLI (inside container)**

If you prefer CLI style, run these from your VM/EC2:

### Step 1: Access MySQL container

```bash
sudo docker exec -it my-compose-app-db-1 mysql -uroot -proot
```

*(If the name differs, run `sudo docker ps` and copy your db container name.)*

---

### Step 2: Use the database

```sql
USE testdb;
```

---

### Step 3: Create a table

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```

---

### Step 4: Insert a sample row

```sql
INSERT INTO users (name, email) VALUES ('sahil', 'sahilshaikh@example.com');
```

---

### Step 5: Verify it

```sql
SELECT * FROM users;
```

Output should be like:

```
+----+--------+---------------------+
| id | name   | email               |
+----+--------+---------------------+
|  1 | Sahil | sahil@example.com    |
+----+--------+---------------------+
```

---

That’s it! 🚀
You now have:

* Flask container ✅
* MySQL container ✅
* phpMyAdmin UI ✅
* Database + table ✅
---

## 🐍 Step 1: Open your Flask file

In your `my-compose-app` folder:

```bash
nano app.py
```

---

## 🧠 Step 2: Replace everything with this code:

```python
from flask import Flask
import os
import mysql.connector

app = Flask(__name__)

@app.route('/')
def home():
    db_host = os.environ.get("DB_HOST", "db")
    db_user = os.environ.get("DB_USER", "root")
    db_pass = os.environ.get("DB_PASSWORD", "root")
    db_name = os.environ.get("DB_NAME", "testdb")

    try:
        conn = mysql.connector.connect(
            host=db_host,
            user=db_user,
            password=db_pass,
            database=db_name
        )
        cursor = conn.cursor()
        cursor.execute("SELECT id, name, email FROM users")
        rows = cursor.fetchall()

        html = "<h2>🐳 Docker Compose App</h2><h3>✅ Connected to MySQL!</h3><br><b>Users Table:</b><br><ul>"
        for row in rows:
            html += f"<li>{row[0]} — {row[1]} — {row[2]}</li>"
        html += "</ul>"

        cursor.close()
        conn.close()
        return html

    except Exception as e:
        return f"<h3>❌ DB Connection failed:</h3> {e}"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

---

## ⚙️ Step 3: Rebuild your container

Since the app code changed:

```bash
sudo docker compose up -d --build
```

---

## 🧩 Step 4: Check logs

To make sure the app started cleanly:

```bash
sudo docker compose logs -f web
```

You should see something like:

```
Running on http://0.0.0.0:5000
```

---

## 🌍 Step 5: Test in browser

Visit:

```
http://<your-public-ip>:5000
```

You should now see something like:

```
🐳 Docker Compose App
✅ Connected to MySQL!
Users Table:
1 — Roshan — roshan@example.com
```

---

## 💡 Step 6: (Optional) Add more users

You can add more rows from phpMyAdmin → Insert tab,
and refresh the Flask page — it’ll instantly reflect new data 🎯
---

### 🧩 Step 1: Create the `requirements.txt`

You’re in the right folder (`~/my-compose-app`), so just run:

```bash
nano requirements.txt
```

Then paste this inside:

```
Flask
mysql-connector-python
```

Save and exit:

* Press **Ctrl + O**, then **Enter**, then **Ctrl + X**

---

### 🧱 Step 2: Verify your folder looks like this

Run:

```bash
ls
```

You should see:

```
Dockerfile
docker-compose.yml
requirements.txt
```

(Optional but recommended: you can add `app.py` and `templates/index.html` later if not already.)

---

### 🐋 Step 3: Rebuild the whole thing

Now that `requirements.txt` exists, do a fresh build:

```bash
sudo docker compose up -d --build
```

---

### 🧠 Step 4: Check running containers

After a minute or two, confirm everything’s running fine:

```bash
sudo docker compose ps
```

Expected output:

```
web          ... Up ... 0.0.0.0:5000->5000/tcp
db           ... Up ... 0.0.0.0:3306->3306/tcp
phpmyadmin   ... Up ... 0.0.0.0:8080->80/tcp
```

---

If that works, your Flask app will be up at:

```
http://<your-EC2-IP>:5000
```

And phpMyAdmin here:

```
http://<your-EC2-IP>:8080
```
