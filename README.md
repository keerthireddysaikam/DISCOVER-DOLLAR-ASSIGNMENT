# Discover Dollar Assignment

## Application accessible on: http://13.60.219.136

This repository contains a full-stack MEAN (MongoDB, Express, Angular, Node.js) application that has been containerized using Docker, orchestrated with Docker Compose, and deployed to an AWS EC2 instance via a CI/CD pipeline using GitHub Actions. Nginx is configured as a reverse proxy to serve the application on Port 80.

## 📂 Project Structure (Folders/Files I have created)

```bash
├── backend/                
│   └── Dockerfile          
├── frontend/               
│   ├── Dockerfile          # Multi-stage docker build
│   └── nginx.conf          
├── nginx/
│   └── default.conf        
├── .github/workflows/
│   └── deploy.yml          
└── docker-compose.yml      
```

## Step 1: Run locally

To run the application on your local machine using Docker:

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/kartik-paliwa1/DISCOVER-DOLLAR-ASSIGNMENT.git
    cd DISCOVER-DOLLAR-ASSIGNMENT
    ```

2.  **Start the containers:**

    ```bash
    docker-compose up --build
    ```

3.  **Access the application:**

      * Frontend: `http://localhost`
      * Backend API: `http://localhost/api`

-----

## Step 2: AWS EC2 Setup 

The application is hosted on an Ubuntu Virtual Machine.

1.  **Launch Instance:**

      * OS: Ubuntu 24.04 LTS.
      * Instance Type: t3.small (Free tier).

2.  **Security Group (Firewall) Rules:**

      * **SSH:** Port 22 (Allowed from My IP).
      * **HTTP:** Port 80 (Allowed from Anywhere 0.0.0.0/0).

3.  **VM Configuration Commands:**

    ```bash
    # Update packages
    sudo apt update && sudo apt upgrade -y

    # Install Docker
    sudo apt install docker.io -y
    sudo usermod -aG docker $USER
    newgrp docker

    # Install Docker Compose (With the help of Documentation)
    sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    ```

-----

## Step 3: Implenting CI/CD Pipeline 

The pipeline is built using **GitHub Actions**. It automatically builds Docker images, pushes them to Docker Hub, and updates the EC2 instance upon every push to the `main` branch.

### Secrets Configuration

The following secrets were added to the GitHub Repository (`Settings > Secrets and variables > Actions`):

| Secret Name | Description |
| :--- | :--- |
| `DOCKER_USERNAME` | Docker Hub Username. |
| `DOCKER_PASSWORD` | Docker Hub Access Token/Password. |
| `HOST_IP` | Public IP address of the AWS EC2 instance. |
| `SSH_USER` | VM Username |
| `SSH_PRIVATE_KEY` | Private SSH Key |

### Pipeline Workflow 

1.  **Checkout Code:** Pulls the latest code from GitHub.
2.  **Login to Docker Hub:** Authenticates using secrets.
3.  **Build & Push:** Builds images for Backend and Frontend, tagging them with the Docker Hub username.
4.  **Deploy to VM:**
      * Copies `docker-compose.yml` and `nginx` config to the VM via SCP.
      * Executes SSH commands to pull the new images and restart containers (`docker-compose up -d`).

-----

## Step 4: Nginx Reverse Proxy Setup

Nginx is used as the single entry point (Gateway) for the application running on Port 80.

  * **File:** `nginx/default.conf`
  * **Routing Logic:**
      * `location /`: Proxies requests to the **Frontend** container.
      * `location /api`: Proxies requests to the **Backend** container.

##### Note: Additionally, a specific `nginx.conf` was added inside the Frontend container to handle Angular SPA routing (redirecting 404s to `index.html` to prevent refresh errors).

-----

## Screenshots & Evidence

### 1\. CI/CD Pipeline Execution

<img width="1470" height="956" alt="Screenshot 2025-11-28 at 3 32 13 PM" src="https://github.com/user-attachments/assets/3a6a16ca-5fd9-4565-9f1c-9c09eab7818b" />


### 2\. Docker Hub Images

<img width="1470" height="956" alt="Screenshot 2025-11-28 at 3 33 39 PM" src="https://github.com/user-attachments/assets/3d9a87f3-009c-452c-b3e4-7425c5f38696" />

### 3\. Application Running on VM

<img width="1470" height="956" alt="Screenshot 2025-11-28 at 3 34 30 PM" src="https://github.com/user-attachments/assets/e6b6cb64-4454-4e83-8a56-306dd8e4c14d" />


### 4\. VM Terminal Output

<img width="1470" height="956" alt="Screenshot 2025-11-28 at 3 35 24 PM" src="https://github.com/user-attachments/assets/305029f6-2e58-4b9b-9fab-b86d88c0281b" />

-----
