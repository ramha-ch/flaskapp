# Flask Application with MySQL using Docker

This guide will walk you through the steps to set up a Flask application with MySQL using Docker, link the containers, and push the application image to Docker Hub.

---

## Prerequisites

- Docker installed on your system.
- A Docker Hub account.

---

## Installation Steps

### Step 1: Install Docker

```bash
sudo apt update
sudo apt install docker.io
```

### Step 2: Verify Docker Installation

```bash
docker ps
```

If you get a permission denied error, run:

```bash
sudo chown $USER /var/run/docker.sock
```

Alternatively:

```bash
sudo chown ubuntu /var/run/docker.sock
```

---

## Setting Up the Application

### Step 3: Clone the Repository

```bash
git clone <repository-url>
```

Remove any existing Dockerfile:

```bash
rm Dockerfile
```

Create a new Dockerfile:

```bash
vi Dockerfile
```

---

## Dockerfile Content

```dockerfile
FROM python:3.9-slim # Use Python 3.9
WORKDIR /app

# Install required packages
RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y gcc default-libmysqlclient-dev pkg-config

# Copy application dependencies
COPY requirements.txt .
RUN pip install mysqlclient
RUN pip install -r requirements.txt

# Copy application source code
COPY . .

# Run the application
CMD ["python", "app.py"]
```

---

## Build and Run Docker Containers

### Step 4: Build the Flask Application Image

```bash
docker build . -t flaskapp
```

### Step 5: Run the Flask Application Container

```bash
docker run -d -p 5000:5000 flaskapp:latest
```

### Step 6: Run MySQL Container

```bash
docker run -d -p 3306:3306 --name mysql \
  -e MYSQL_ROOT_PASSWORD="admin" \
  mysql:5.7
```

---

## Linking Flask and MySQL Containers

### Step 7: Create a Network

```bash
docker network create twotier
```

### Step 8: Relaunch Containers on the Network

Kill the existing containers:

```bash
docker kill <mysql-container-id>
docker kill <flaskapp-container-id>
```

Run the containers again:

For Flask:

```bash
docker run -d -p 5000:5000 --network=twotier \
  -e MYSQLHOST=mysql \
  -e MYSQLUSER=admin \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DB=myDb \
  --name=flaskapp flaskapp:latest
```

For MySQL:

```bash
docker run -d -p 3306:3306 --network=twotier \
  -e MYSQLHOST=mysql \
  -e MYSQLUSER=admin \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DB=myDb \
  --name=mysql mysql:5.7
```

---

## Interact with the MySQL Container

### Step 9: Access MySQL Container

```bash
docker exec -it mysql bash
```

Login to MySQL:

```bash
mysql -u root -p
```

Password: `admin`

### Step 10: Create a Database Table

Use the database:

```sql
USE myDb;
```

Create a table:

```sql
CREATE TABLE messages (
  id INT AUTO_INCREMENT PRIMARY KEY,
  message TEXT
);
```

---

## Test the Application

- Access the application on port `5000`.
- Submit a message in the app.
- Verify the message is saved in the database by querying the `messages` table.

---

## Push to Docker Hub

### Step 11: Login to Docker Hub

```bash
docker login
```

Provide your username and password.

### Step 12: Tag and Push the Image

```bash
docker tag flaskapp:latest <your-dockerhub-username>/flaskapp:latest
docker push <your-dockerhub-username>/flaskapp:latest
```

---

All done!
