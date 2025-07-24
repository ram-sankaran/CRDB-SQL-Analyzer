#  MySQL 5.7 Setup on MacBook Using Colima (No Docker Desktop) and cockroach DB on Macbook

---

## 📄 Prerequisites

- Homebrew installed (`brew`)
- MacBook with M1/M2/M3 or Intel chip
- No Docker Desktop (using Colima)

---

##  Install Colima and Docker CLI

```bash
brew install colima
brew install docker
```

---

## Link Docker CLI and Set PATH

```bash
brew link --overwrite docker

# Add Homebrew bin to your path if needed

echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Verify docker CLI:

```bash
docker --version
```

---

## Start Colima

```bash
colima start
```

Check Colima status:

```bash
colima status
```

Take note of the Docker socket (e.g., `/Users/yourusername/.colima/default/docker.sock`).

---

## Set Correct Docker Host

```bash
echo 'export DOCKER_HOST=unix:///Users/$(whoami)/.colima/default/docker.sock' >> ~/.zshrc
source ~/.zshrc
```

Verify Docker connection:

```bash
docker ps
```

---

## Fix Docker Credential Helper Error

```bash
echo '{}' > ~/.docker/config.json
```

---

## Pull and Run MySQL 5.7 (Apple Silicon Friendly)

```bash
docker run -d \
  --platform linux/amd64 \
  --name mysql57 \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -p 3306:3306 \
  mysql:5.7 \
  --server-id=3 \
  --log-bin=log-bin \
  --binlog-format=ROW \
  --binlog-row-image=FULL \
  --enforce-gtid-consistency=ON \
  --gtid-mode=ON
```

---

## Connect to MySQL CLI

```bash
docker exec -it mysql57 mysql -uroot -p
```

Password: `my-secret-pw`

Example MySQL command:

```sql
SHOW DATABASES;
```

---

## Useful Docker Commands

```bash
docker ps            # List running containers
docker stop mysql57  # Stop the container
docker start mysql57 # Start the container again
docker logs mysql57  # View container logs
docker rm -f mysql57 # Remove (delete) the container
```

---

# Optional: Docker Compose Setup

Create a `docker-compose.yml`:

```yaml
services:
  mysql57:
    image: mysql:5.7
    platform: linux/amd64
    container_name: mysql57
    environment:
      MYSQL_ROOT_PASSWORD: my-secret-pw
    ports:
      - "3306:3306"
    command:
      [
        "--server-id=3",
        "--log-bin=log-bin",
        "--binlog-format=ROW",
        "--binlog-row-image=FULL",
        "--enforce-gtid-consistency=ON",
        "--gtid-mode=ON"
      ]
    volumes:
      - mysql_data:/var/lib/mysql
    restart: unless-stopped

volumes:
  mysql_data:

```

Then start services:

```bash
docker-compose up -d
```

Stop services:

```bash
docker-compose down
```

Create a sample dataset in MySql: 

Connect to your MySQL:

```bash
docker exec -it mysql57 mysql -uroot -p 
```
Then inside MySQL terminal, paste:


```bash
 -- Create database
CREATE DATABASE IF NOT EXISTS testdb;
USE testdb;

-- Create users table
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create orders table
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    product_name VARCHAR(100),
    amount DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Insert sample users
INSERT INTO users (username, email) VALUES
('alice', 'alice@example.com'),
('bob', 'bob@example.com'),
('carol', 'carol@example.com');

-- Insert sample orders
INSERT INTO orders (user_id, product_name, amount) VALUES
(1, 'iPhone', 999.99),
(2, 'MacBook', 1999.99),
(1, 'AirPods', 199.99),
(3, 'iPad', 499.99);

-- Create replication user for streaming
CREATE USER 'repl'@'%' IDENTIFIED BY 'replpassword';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;

```

---

# Start the cockroach DB single node on the Mac book as target 

```bash

cockroach start-single-node --insecure --listen-addr=localhost:26257 --http-addr=localhost:8080 --background

```

Connect to CockroachDB SQL:

```bash

cockroach sql --insecure --host=localhost:26257

```

Create the target Database in cockroach DB


```bash

CREATE DATABASE testdb;

```

Install MOLT CLI : https://www.cockroachlabs.com/docs/molt/molt-fetch#installation

```bash
chmod +x ~/Downloads/molt-darwin-arm64
sudo mv ~/Downloads/molt-darwin-arm64 /usr/local/bin/molt
molt -v

```

# Environment Setup Complete! 
