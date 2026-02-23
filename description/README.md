# Project java_cicd Description portifolio

# End-to-End CI/CD Project from Scratch (AWS)

This project demonstrates a production-style CI/CD implementation on Amazon Web Services (AWS) using Jenkins, SonarQube, Nexus, OWASP Dependency-Check, Docker, and a simple CD flow. It covers building a Java application, running quality and security scans, building and pushing a Docker image to Docker Hub, then deploying the container (on the Jenkins host for demo purposes).

> Note: Deploying on the same Jenkins server works for learning and demos, but is not production recommended. In production, deploy to a separate server or use Kubernetes/ECS.

---

## What this project delivers

- CI pipeline: checkout -> compile -> SonarQube analysis -> OWASP dependency scan -> package -> Docker build/push -> trigger CD
- CD pipeline: pull and run the Docker image as a container
- Two-server setup:
  - Server A: Jenkins + Docker (CI/CD + deployment target in this demo)
  - Server B: SonarQube + Nexus (running in Docker containers)

---

## Architecture

### High-level components

- GitHub: source code repository
- Jenkins (Server A): runs CI and triggers CD
- Docker Hub: stores Docker images
- SonarQube (Server B): static code analysis and quality gates
- Nexus (Server B): artifact repository (optional in this pipeline but included in the architecture)

### Diagram
Add the architecture image to your repo and reference it here.

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/java_cicd-Architecture.png)

Recommended structure:
Use kubernetes, the modern architecture though VM and docker still highly used!

## Pipeline summary

### CI Pipeline (job: `java_cicd`)
1. Git Checkout (pulls from GitHub `main`)
2. Compile (Maven)
3. SonarQube Analysis (SonarScanner)
4. OWASP Scan (Dependency-Check + publish report)
5. Build Application (package jar, tests skipped in CI demo)
6. Build and Push Docker Image (Docker build, tag, push to Docker Hub)
7. Trigger CD Pipeline (`java_CD` job)

### CD Pipeline (job: `java_CD`)
1. Deploy Docker container on the Jenkins agent host (Server A)
   - Runs the image from Docker Hub
   - Exposes the application on port `8070`

---




## Jenkins plugins used

Install these in Jenkins (Manage Jenkins -> Plugins):

- Pipeline (Declarative Pipeline)
- Git plugin
- Credentials Binding
- SonarQube Scanner for Jenkins
- OWASP Dependency-Check plugin
- Docker Pipeline plugin

---

## Servers and ports

### Server A (Ubuntu): Jenkins + Docker + Deployment Host
- Jenkins: `8080` (recommended behind reverse proxy + SSL)
- Application container: `8070` (or bind to localhost and reverse proxy)

### Server B (Ubuntu): SonarQube + Nexus (Docker)
- SonarQube: `9000`
- Nexus: `8081`

---

## Prerequisites

- AWS account and 2 Ubuntu EC2 instances (or 1 instance for demo)
- Domain/subdomains if you plan reverse proxy + SSL (optional for the demo)
- Docker installed on Server A (and Server B if running tools in containers)
- Jenkins installed on Server A
- SonarQube and Nexus running on Server B (Docker Compose recommended)
- Git installed
- Java and Maven configured in Jenkins Tools:
  - JDK: `jdk11`
  - Maven: `maven3`
- SonarScanner configured in Jenkins Tools:
  - Name: `sonar-scanner`

---

## Credentials required (Jenkins)

Create these credentials in Jenkins (Manage Jenkins -> Credentials):

### 1) Docker Hub credentials
- ID: `mydocker`
- Type: Username with password
- Used to push `lipatrick/java_cicd:latest`

### 2) SonarQube token
- Generate token in SonarQube (My Account -> Security -> Tokens)
- Store it securely in Jenkins (recommended):
  - Either as Secret Text (example ID: `sonar-token`)
  - Or inline temporarily (not recommended)

---

## Repository structure (example)

- `Jenkinsfile` (CI pipeline)
- `docker/Dockerfile`
- `pom.xml`
- `src/...`

---

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-22%2022-18-02.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2012-31-33.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2012-38-33.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2012-56-45.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2013-06-33.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2014-02-16.png)

## Running the project

### Step 1: Push code to GitHub
Push your application source code and Jenkinsfile to GitHub.

### Step 2: Configure Jenkins Tools
In Jenkins:
- Manage Jenkins -> Tools
  - Configure JDK: `jdk11`
  - Configure Maven: `maven3`
  - Configure SonarScanner: `sonar-scanner`

### Step 3: Create the CI pipeline job
- Create Pipeline job: `java_cicd`
- Point to your repository OR paste Jenkinsfile script
- Ensure the job can access Docker (jenkins user in docker group)

### Step 4: Create the CD pipeline job
- Create Pipeline job: `java_CD`
- CD pipeline runs `docker run` to deploy the pushed image

### Step 5: Run CI
Trigger `java_cicd`
- It will push the image to Docker Hub
- It will trigger `java_CD` to deploy the container

### Step 6: Access the application
If deployed on Server A:
- `http://SERVER_A_PUBLIC_IP:8070`

If you use reverse proxy (recommended):
- `https://app.yourdomain.com`

---



![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-23%2012-27-53.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-23%2012-28-20.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-22%2018-32-35.png)



![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-22%2021-32-22.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-22%2022-06-38.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-22%2022-06-59.png)



## Production recommendations

For real production deployments:

- Deploy on a separate VM (Server C) instead of Jenkins server
- Use `docker compose` on the production VM and deploy by SSH from Jenkins:
  - `docker compose pull && docker compose up -d`
- Tag images with build numbers for rollback:
  - `lipatrick/java_cicd:${BUILD_NUMBER}` and `latest`
- Restrict ports:
  - Expose only `80/443`
  - Keep SonarQube and Nexus private (VPN, security groups, or localhost binding)
- Add TLS/SSL:
  - Reverse proxy with Apache/Nginx + Certbot
- Add monitoring/logging:
  - Node exporter + Prometheus + Grafana (or alternatives)
  - Central logs (optional)

---

## Troubleshooting

### Docker permission denied in Jenkins
Ensure:
- Docker is running: `sudo systemctl status docker`
- Jenkins user is in docker group:
  - `sudo usermod -aG docker jenkins`
  - `sudo systemctl restart jenkins`
- Validate:
  - `sudo -u jenkins docker ps`

### SonarScanner not found
- Check Manage Jenkins -> Tools -> SonarScanner installation name matches `sonar-scanner`
- Validate `$SCANNER_HOME/bin/sonar-scanner` exists

### Application not reachable on port 8070
- Confirm container is running:
  - `docker ps`
- Confirm port mapping:
  - `docker port <container_name>`
- Open AWS Security Group inbound rule for port `8070` (demo only)
- Prefer reverse proxy on `80/443` in production

---

## License
For learning and demonstration purposes.











