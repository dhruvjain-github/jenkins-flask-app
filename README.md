 
# Jenkins Pipeline Test Application

This is a temporary Flask application with MySQL database used for testing Jenkins CI/CD pipeline functionality. The application serves as a simple two-tier architecture to demonstrate automated build, push, and deployment processes using Jenkins.

## Jenkins Pipeline Overview

This project includes a Jenkins pipeline (`Jenkinsfile`) with Git SCM integration that automates:

1. **Clone Stage**: Pulls code from the GitHub repository
2. **Build and Push Stage**: Builds Docker image and pushes to DockerHub
3. **Pull and Deploy Stage**: Pulls the image on a different agent and deploys using docker-compose

### Key Features:
- **Automatic Triggering**: Pipeline automatically builds whenever code is pushed to the repository
- **Git SCM Integration**: Integrated with GitHub for seamless CI/CD workflow
- **Email Notifications**: Sends email alerts when pipeline fails
- **Multi-Agent Deployment**: Uses two Jenkins agents (`agent-1` and `agent-2`) to simulate real-world CI/CD workflow

## Prerequisites

Before you begin, make sure you have the following installed and configured:

- Docker
- Jenkins with configured agents (agent-1 and agent-2)
- DockerHub credentials configured in Jenkins
- Git SCM integration configured in Jenkins
- Email server configuration for failure notifications
- Git (optional, for cloning the repository)

## Jenkins Pipeline Setup

1. Configure Jenkins with two agents labeled `agent-1` and `agent-2`

2. Add DockerHub credentials to Jenkins:
   - Go to Jenkins → Manage Jenkins → Manage Credentials
   - Add a Username/Password credential with ID: `DockerHub Credentials`

3. Configure Git SCM Integration:
   - Install Git plugin and GitHub plugin in Jenkins
   - Configure webhook in GitHub repository pointing to your Jenkins instance
   - Set up polling SCM or webhook triggers for automatic builds

4. Configure Email Notifications:
   - Go to Jenkins → Manage Jenkins → Configure System
   - Set up SMTP server details for email notifications
   - Configure email recipients for build failure notifications

5. Create a new Jenkins Pipeline job:
   - Point it to this repository: `https://github.com/dhruvjain-github/jenkins-flask-app.git`
   - Enable "GitHub hook trigger for GITScm polling" or configure polling schedule
   - Set up post-build actions for email notifications on failure

6. The pipeline will automatically:
   - **Trigger on Push**: Automatically start when code is pushed to the repository
   - Clone the repository on agent-1
   - Build and push Docker image to DockerHub
   - Pull and deploy the application on agent-2
   - **Send Email on Failure**: Notify configured recipients if any stage fails

## Manual Setup (Alternative)

1. Clone this repository:

   ```bash
   git clone https://github.com/dhruvjain-github/jenkins-flask-app.git
   ```

2. Navigate to the project directory:

   ```bash
   cd jenkins-flask-app
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

## Application Usage (For Testing)

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

To stop and remove the Docker containers,  press `Ctrl+C` in the terminal where the containers are running, or use the following command:

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

## Notes

- **This is a temporary testing application** created specifically for Jenkins pipeline testing purposes.

- **Automatic CI/CD**: The pipeline automatically triggers on every git push, demonstrating continuous integration and deployment.

- **Email Notifications**: Configured to send email alerts to designated recipients when pipeline fails, ensuring quick response to issues.

- The application uses hardcoded MySQL credentials (root/admin) which is acceptable for testing but should never be used in production.

- This setup demonstrates a complete CI/CD workflow: **Code Push → Auto Build → Push to Registry → Deploy → Notify on Failure**.

- The pipeline is configured to work with the repository: `https://github.com/dhruvjain-github/jenkins-flask-app.git`

- **Git SCM Integration**: Seamlessly integrates with GitHub for webhook-based or polling-based automatic builds.

- For production environments, implement proper security practices, environment-specific configurations, comprehensive testing stages, and secure notification methods.

- If you encounter issues during pipeline execution, check Jenkins logs, Docker container logs, and email configuration for troubleshooting.

## Testing Purpose Only

⚠️ **Important**: This application is created solely for testing Jenkins pipeline functionality with Git SCM integration and automated email notifications. It should not be used in production environments.

### Pipeline Features Demonstrated:
- ✅ Git SCM Integration (Auto-trigger on push)
- ✅ Multi-stage CI/CD Pipeline
- ✅ Docker Image Build & Push
- ✅ Multi-Agent Deployment
- ✅ Email Notifications on Failure

```

