# Jenkins CI/CD Pipeline with Docker and AWS EC2

## Project Overview

This project demonstrates an end-to-end CI/CD pipeline using **GitHub, Jenkins, Docker, Nginx, and AWS EC2**.

Whenever a developer pushes code to the `main` branch, GitHub sends a webhook to Jenkins. Jenkins automatically pulls the latest source code, builds a new Docker image, removes the previous application container, and deploys the updated version on AWS EC2.

The complete pipeline was successfully tested by upgrading the application from **Version 1.0 to Version 2.0** without manually triggering Jenkins.

---

## Architecture

```text
Developer
    |
    | Git Push
    v
GitHub Repository
    |
    | Webhook
    v
Jenkins
(AWS EC2)
    |
    | Checkout Source
    v
Docker Build
    |
    v
Docker Image
    |
    | Deploy
    v
Nginx Container
    |
    v
Web Application
Port 80
```

---

## Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Jenkins and Docker host |
| Amazon Linux 2023 | Server operating system |
| GitHub | Source code repository |
| Git | Source code management |
| Jenkins | CI/CD automation |
| Docker | Containerization |
| Nginx | Web server |
| Java 21 | Jenkins runtime |
| GitHub Webhook | Automatic pipeline trigger |

---

## Repository Structure

```text
jenkins-cicd-project/
├── Dockerfile
├── Jenkinsfile
├── README.md
├── index.html
└── docs/
    └── Jenkins_CICD_Project_Complete_Notes.docx
```

---

## CI/CD Workflow

The complete deployment workflow is:

```text
Code Change
     ↓
Git Commit
     ↓
Push to GitHub
     ↓
GitHub Webhook
     ↓
Jenkins Pipeline Triggered
     ↓
Checkout Latest Code
     ↓
Build Docker Image
     ↓
Remove Previous Container
     ↓
Deploy New Container
     ↓
Updated Application Live
```

---

## Jenkins Pipeline

The Jenkins pipeline performs three major operations:

### 1. Checkout

Jenkins retrieves the latest source code from GitHub.

```groovy
checkout scm
```

### 2. Build Docker Image

```bash
docker build -t jenkins-cicd-app .
```

This builds the application Docker image using the Dockerfile.

### 3. Deploy Application

```bash
docker rm -f jenkins-cicd-app || true

docker run -d \
  --name jenkins-cicd-app \
  -p 80:80 \
  jenkins-cicd-app
```

The previous container is removed and a new container is deployed with the latest application version.

---

## Docker Configuration

The application uses Nginx as the web server.

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html
```

The Docker container exposes the application through EC2 port `80`.

---

## GitHub Webhook

GitHub Webhook is configured to automatically notify Jenkins whenever code is pushed to the repository.

Webhook endpoint format:

```text
http://<JENKINS-SERVER>:8080/github-webhook/
```

Event:

```text
Push Event
```

Jenkins trigger:

```text
GitHub hook trigger for GITScm polling
```

Because of this integration, Jenkins does not need to be manually started using **Build Now** after every code change.

---

## Deployment Verification

The initial application displayed:

```text
Version: 1.0
```

The application was then changed in GitHub to:

```text
Version: 2.0 - Auto Deployment
```

After committing the change:

```text
GitHub Push
      ↓
Webhook Triggered
      ↓
Jenkins Build Started Automatically
      ↓
Docker Image Rebuilt
      ↓
Container Replaced
      ↓
Version 2.0 Deployed
```

No manual Jenkins build was required.

---

## Troubleshooting Performed

During implementation, several real-world issues were investigated and resolved.

### EC2 Memory Limitation

The initial EC2 instance had limited memory while Jenkins and Docker were being configured.

Memory was checked using:

```bash
free -h
```

The lab instance was resized to provide additional memory for Jenkins and Docker workloads.

### Jenkins Built-In Node Offline

Jenkins reported that the Built-In Node was temporarily offline.

Disk and inode usage were checked:

```bash
df -h
df -i
```

Jenkins logs were investigated using:

```bash
sudo journalctl -u jenkins -n 100 --no-pager -l
```

The issue was related to Jenkins disk/temp-space monitoring and was corrected after verifying actual server capacity.

### Docker Permission

Jenkins required access to the Docker daemon.

Verification:

```bash
id jenkins
sudo -u jenkins docker ps
```

This confirmed that Jenkins could execute Docker commands.

### GitHub Webhook Connectivity

The initial webhook delivery could not connect to Jenkins.

The following were verified:

- Jenkins service status
- EC2 network connectivity
- Security Group rules
- Jenkins port `8080`
- Webhook URL

After correcting connectivity, GitHub webhook delivery succeeded.

---

## Security Considerations

This project is designed as a hands-on CI/CD lab.

For a production environment, the architecture should additionally include:

- HTTPS/TLS for Jenkins
- Restricted Jenkins administrative access
- Dedicated Jenkins build agents
- Secure credentials management
- Private networking where appropriate
- Container image registry such as Amazon ECR
- Automated testing and security scanning
- Monitoring and alerting
- High availability
- Automated rollback strategy
- Infrastructure as Code

---

## Key Learning Outcomes

Through this project I gained hands-on experience with:

- Jenkins Pipeline as Code
- GitHub and Jenkins integration
- GitHub Webhooks
- Docker image creation
- Docker container deployment
- AWS EC2 administration
- Linux troubleshooting
- Jenkins service troubleshooting
- Docker permissions
- CI/CD automation
- Network and Security Group troubleshooting

---

## Complete Project Documentation

Detailed implementation notes, commands, troubleshooting steps, and explanations are available here:

[Download Complete Jenkins CI/CD Project Documentation](docs/Jenkins_CICD_Project_Complete_Notes.docx)

---

## Final Result

The successfully implemented pipeline is:

```text
GitHub
   ↓
Webhook
   ↓
Jenkins
   ↓
Docker Build
   ↓
Docker Deployment
   ↓
AWS EC2
   ↓
Updated Web Application
```

**Final Test:** Version 1.0 → Version 2.0 automatically deployed.

**Project Status: Successfully Completed**
