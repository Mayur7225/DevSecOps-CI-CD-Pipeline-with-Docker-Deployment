# 🚀 DevSecOps CI/CD Pipeline for Node.js Todo App

This project demonstrates a complete DevSecOps CI/CD Pipeline built using:

- Jenkins  
- SonarQube  
- GitLeaks (Secrets Scan)  
- Trivy (Dependency & Image Scan)  
- Docker  
- Docker Hub  
- Docker Compose  

It covers both CI + CD + Security (DevSecOps).

---

# 🏗 Architecture Diagram
![Architecture](./devsecops-pipeline.png)

---

# 🔥 Features (DevSecOps)

| Feature | Tool |
|--------|------|
| SAST (Code Quality & Security) | SonarQube |
| Secret Detection | GitLeaks |
| Dependency Scan | Trivy FS |
| Docker Image Scan | Trivy Image |
| CI/CD Pipeline | Jenkins |
| Deployment | Docker Compose |
| Artifact Storage | Docker Hub |

---

# 🔄 Pipeline Workflow

# 1️⃣ Checkout Source Code
Pulls code from GitHub branch `main`.

# 2️⃣ Install Node Modules
Runs:

npm install

# 3️⃣ SonarQube SAST Scan
Static analysis of:
- Bugs  
- Code smells  
- Vulnerabilities  

# 4️⃣ GitLeaks Secret Scan
Detects:
- API Keys  
- Passwords  
- Tokens  

# 5️⃣ Trivy File System Scan
Scans Node dependencies from `node_modules`.

# 6️⃣ Build Docker Image
Creates production-ready image:


# 7️⃣ Trivy Image Scan
Finds HIGH & CRITICAL vulnerabilities inside the Docker image.

# 8️⃣ Push to Docker Hub
Image is uploaded to DockerHub.

# 9️⃣ Deployment
Application is deployed using Docker Compose.

---

# 🐳 Docker Commands (If You Want to Run Locally)

# Run App

