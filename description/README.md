# Project java_cicd Description portifolio

![image alt](https://github.com/lippatrick/java_cicd/blob/main/description/images/java_cicd-Architecture.png)

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








