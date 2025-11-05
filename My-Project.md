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
## 🧹 Step 7: Cleanup when done

```bash
sudo docker compose down -v
```

(`-v` removes the volume too, so MySQL data resets.)

