# Exercise 7: Jenkins CI Automation

## Aim
To set up Jenkins using Docker and access the Jenkins dashboard to understand the basics of Continuous Integration (CI).

## Tools and Technologies Used
- **Jenkins** – Automation server used for Continuous Integration and Continuous Delivery.
- **Docker** – Platform used to run Jenkins in a container.
- **Windows PowerShell** – Used to execute Docker commands.
- **Web Browser** – Used to access the Jenkins dashboard.

## Procedure

### Step 1: Verify Docker Installation
Verified that Docker was installed and available using:

```powershell
docker --version
```

### Step 2: Obtain the Jenkins Docker Image
Used the Jenkins Long-Term Support (LTS) Docker image to run Jenkins in a container.

```powershell
docker pull jenkins/jenkins:lts
```

*Note: If the image was already available locally, downloading it again was not necessary.*

### Step 3: Run Jenkins in a Docker Container
Created and started a Jenkins container with the following command:

```powershell
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

**Port mapping:**
- `8080:8080` – Exposes the Jenkins web interface on the host machine.
- `50000:50000` – Exposes the port commonly used for inbound Jenkins agent connections.

### Step 4: Verify the Container
Checked the running Docker containers using:

```powershell
docker ps
```

Verified that the Jenkins container was running and that the required ports were mapped correctly.

### Step 5: Access Jenkins
Opened the following URL in a web browser:

```text
http://localhost:8080
```

Completed the initial Jenkins setup, where required, and logged in to the Jenkins dashboard.

## Result
Successfully deployed Jenkins using Docker on the local Windows machine and accessed the Jenkins dashboard through a web browser.

## Conclusion
This exercise provided practical experience in running Jenkins inside a Docker container, configuring port mappings, verifying container status, and accessing the Jenkins web interface. Jenkins can be used as a foundation for automating software builds and other Continuous Integration tasks.

## References
- [Jenkins Official Website](https://www.jenkins.io/)
- [Jenkins Docker Image](https://hub.docker.com/r/jenkins/jenkins)
- [Docker Documentation](https://docs.docker.com/)
- [DevOps Lab – Exercise 7: Jenkins CI Automation](https://github.com/SunagP/DevOps-Lab/blob/main/Exercises/7-Jenkins-CI-Automation.md)
