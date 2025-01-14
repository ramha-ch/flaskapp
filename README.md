 
# Flask App with MySQL Docker Setup

This is a simple Flask app that interacts with a MySQL database. The app allows users to submit messages, which are then stored in the database and displayed on the frontend.

## Prerequisites

Before you begin, make sure you have the following installed:

- Docker
- Git (optional, for cloning the repository)

## Setup

1. Clone this repository (if you haven't already):

   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```

2. Navigate to the project directory:

   ```bash
   cd your-repo-name
   ```

3. Create a `.env` file in the project directory to store your MySQL environment variables:

   ```bash
   touch .env
   ```

4. Open the `.env` file and add your MySQL configuration:

   ```
   MYSQL_HOST=mysql
   MYSQL_USER=your_username
   MYSQL_PASSWORD=your_password
   MYSQL_DB=your_database
   ```

## Usage

1. Start the containers using Docker Compose:

   ```bash
   docker-compose up --build
   ```

2. Access the Flask app in your web browser:

   - Frontend: http://localhost
   - Backend: http://localhost:5000

3. Create the `messages` table in your MySQL database:

   - Use a MySQL client or tool (e.g., phpMyAdmin) to execute the following SQL commands:
   
     ```sql
     CREATE TABLE messages (
         id INT AUTO_INCREMENT PRIMARY KEY,
         message TEXT
     );
     ```

4. Interact with the app:

   - Visit http://localhost to see the frontend. You can submit new messages using the form.
   - Visit http://localhost:5000/insert_sql to insert a message directly into the `messages` table via an SQL query.

## Cleaning Up

To stop and remove the Docker containers, press `Ctrl+C` in the terminal where the containers are running, or use the following command:

```bash
docker-compose down
```

## To run this two-tier application using  without docker-compose

- First create a docker image from Dockerfile
```bash
docker build -t flaskapp .
```

- Now, make sure that you have created a network using following command
```bash
docker network create twotier
```

- Attach both the containers in the same network, so that they can communicate with each other

i) MySQL container 
```bash
docker run -d \
    --name mysql \
    -v mysql-data:/var/lib/mysql \
    --network=twotier \
    -e MYSQL_DATABASE=mydb \
    -e MYSQL_ROOT_PASSWORD=admin \
    -p 3306:3306 \
    mysql:5.7

```
ii) Backend container
```bash
docker run -d \
    --name flaskapp \
    --network=twotier \
    -e MYSQL_HOST=mysql \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=admin \
    -e MYSQL_DB=mydb \
    -p 5000:5000 \
    flaskapp:latest

```
## Setup  

### 1. Clone the Repository  
```bash  
git clone https://github.com/your-username/your-repo-name.git  
cd your-repo-name  
2. Configure Environment Variables
Create a .env file in the project directory:

bash
Copy code
touch .env  
Add the following variables to the .env file:

env
Copy code
MYSQL_HOST=mysql  
MYSQL_USER=your_username  
MYSQL_PASSWORD=your_password  
MYSQL_DB=your_database  
3. Build and Run the Containers
Use Docker Compose to build and start the application:

bash
Copy code
docker-compose up --build  
4. Pull the Docker Image (Optional)
If the image is already available on Docker Hub, you can pull it directly instead of building it:

bash
Copy code
docker pull your-dockerhub-username/flask-mysql-app:latest  
Then, run the container:

bash
Copy code
docker run -d -p 5000:5000 --name flask-mysql-app your-dockerhub-username/flask-mysql-app:latest  
Database Setup
To create the messages table in your MySQL database, use a MySQL client or tool and execute the following SQL commands:

sql
Copy code
CREATE TABLE messages (  
    id INT AUTO_INCREMENT PRIMARY KEY,  
    message TEXT  
);

1. Build the Docker Image
Ensure your Dockerfile and application are correctly set up. Navigate to your project directory containing the Dockerfile. Then build the Docker image:

bash
Copy code
docker build -t your-dockerhub-username/flask-mysql-app:latest .  
Replace your-dockerhub-username with your Docker Hub username.
latest can be replaced with a specific tag if needed.
2. Run Tests and Verify
Before pushing, make sure everything works as expected:

bash
Copy code
docker run -d -p 5000:5000 your-dockerhub-username/flask-mysql-app:latest  
3. Tag the Image
Tag the Docker image with your Docker Hub username and repository name:

bash
Copy code
docker tag your-dockerhub-username/flask-mysql-app:latest your-dockerhub-username/flask-mysql-app:version1.0  
4. Push the Image to Docker Hub
Push the tagged image to Docker Hub:

bash
Copy code
docker push your-dockerhub-username/flask-mysql-app:version1.0  
This will upload the Docker image to your Docker Hub repository under the specified tag (version1.0).

Ensure that you have logged into Docker Hub by running:

bash
Copy code
docker login  
Use your Docker Hub username and password to authenticate.

## Notes

- Make sure to replace placeholders (e.g., `your_username`, `your_password`, `your_database`) with your actual MySQL configuration.

- This is a basic setup for demonstration purposes. In a production environment, you should follow best practices for security and performance.

- Be cautious when executing SQL queries directly. Validate and sanitize user inputs to prevent vulnerabilities like SQL injection.

- If you encounter issues, check Docker logs and error messages for troubleshooting.

```

