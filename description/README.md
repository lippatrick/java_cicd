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

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-22%2022-18-02.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2012-31-33.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2012-38-33.png)

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2012-56-45.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2013-06-33.png)


![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/Screenshot%20from%202026-02-19%2014-02-16.png)

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










