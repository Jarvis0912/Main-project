# End-to-End DevOps CI/CD Pipeline (Capstone Project)

## Project Description
This repository contains the source code and infrastructure configuration for a fully automated DevOps pipeline. It demonstrates the continuous integration and continuous deployment (CI/CD) of a containerized Node.js web application. The application is automatically built, pushed to a container registry, and deployed to an AWS EC2 production environment, with infrastructure health actively monitored using a custom metric stack.

## Tech Stack
* **Application Core:** Node.js, Express.js
* **Containerization:** Docker, Docker Hub
* **CI/CD Automation:** Jenkins (Pipeline as Code)
* **Infrastructure:** AWS EC2 (Ubuntu Linux)
* **Monitoring & Observability:** Prometheus, Grafana, Node Exporter
* **Version Control:** Git, GitHub

## Setup Instructions (Local Deployment)
To build and run this application locally on your machine, ensure Docker is installed and run the following commands:

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/jarvis0912/Main-project.git](https://github.com/jarvis0912/Main-project.git)
   cd Main-project

   #Build the Docker image
   docker build -t devops-capstone-app:latest .

   #Run the container locally
   docker run -d -p 3000:3000 --name local-web-app devops-capstone-app:latest

   #Access the application:

Web App: http://13.203.76.176:3000

Health/Metrics Endpoint: http://13.203.76.176:3000


CI/CD Flow Explained
This project utilizes a fully automated declarative Jenkins pipeline (Jenkinsfile) with the following workflow:

1. Continuous Integration (Trigger & Checkout): A GitHub webhook detects any code pushed to the main branch and automatically triggers the Jenkins pipeline. Jenkins securely clones the latest source code.

2. Build Stage: Jenkins reads the Dockerfile, installs Node.js dependencies, and packages the application into a new Docker container image tagged with the specific build number.

3. Delivery Stage: Jenkins securely logs into Docker Hub and pushes the newly built container image to the public registry (jarvis0912/devops-capstone-app).

4. Deployment Stage: Jenkins authenticates with the production AWS EC2 server via SSH, pulls the latest image from Docker Hub, gracefully stops the old container, and starts the new application container.

5. Continuous Monitoring: Prometheus continuously scrapes the live /metrics endpoint of the deployed container and hardware metrics from Node Exporter, visualizing the data in real-time via Grafana dashboards.

