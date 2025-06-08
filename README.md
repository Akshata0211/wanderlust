# sec-cicd-jenkins

This repository sets up a **CI/CD pipeline** for secure and efficient application development using the following tools:

- **Jenkins**: Automates the continuous integration and deployment (CI/CD) process.
- **SonarQube**: Static code analysis tool for ensuring code quality.
- **Trivy**: Vulnerability scanning tool for container images.
- **OWASP Dependency-Check**: Scans for known vulnerabilities in project dependencies.
- **Docker**: Containerization for a consistent and portable environment.

## Prerequisites

Ensure that you have an **AWS EC2 instance** (Ubuntu) with the following specs:
- **Instance Type**: t2.large
- **Root Volume**: 20GB

---

### Step 1: Docker Installation
To install Docker and Docker Compose, run:

```bash
sudo apt-get install docker.io -y
sudo apt-get install docker-compose -y
```
Give Docker permission to run with your user:

```bash
whoami
sudo usermod -aG docker $(whoami)
sudo reboot
```

### Step 2: Jenkins Installation
Install necessary dependencies:

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
```

Add Jenkins repository:

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
```

Check Jenkins status:
```bash
systemctl status jenkins

```

Get Jenkins initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Step 3: SonarQube Setup
Run SonarQube using Docker:

```bash
docker run -itd --name sonarqube-server -p 9000:9000 sonarqube:lts-community
```

### Step 4: Trivy Installation
To install Trivy:

```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```

Step 5: Jenkins Plugin Setup
Install plugins: `manage jenkins > plugins`
- SonarQube Scanner
- Sonar Quality Gates
- OWASP Dependency-Check
- Docker

Give Jenkins permission to access Docker and Restart:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart docker
sudo systemctl restart jenkins
```

### Step 6: Security Group Configuration
Ensure your security groups in AWS EC2 allow the following ports:
- Jenkins: 8080
- SonarQube: 9000
- Web Application: 5173

### Step 7: SonarQube Configuration

1. Create a token on SonarQube:
  - Go to `Administration > Security > Users > Administrator`
  - Generate a new token and copy it.
2. Set up a Webhook in SonarQube to notify Jenkins:
  - Go to `Administration > Configuration > Webhooks`
  - Enter the URL: `http://ec2-public-ip:8080/sonarqube-webhook/`

### Step 8: Jenkins Configuration

In Jenkins:

1. Add SonarQube Token:
  - Go to `Jenkins Dashboard > Manage Jenkins > Manage Credentials > (Select Global) > Add Credentials`
  - Choose `Kind: Secret Text` then Enter a name, such as Sonar in the ID field and enter the SonarQube token.

2. Configuration in Jenkins:
  - Go to `Jenkins Dashboard > Manage Jenkins > Configure System`
  - Click add Jenkins URL
   - Jekins URL: `http://<your-ec2-public-ip>:8080/`
  - Click add SonarQube Scanner.
    - In `SonarQube servers`, enter the details of your SonarQube server:
       - Name: Sonar
       - URL: `http://<your-ec2-public-ip>:9000/` (SonarQube URL).
       - Token: Select the Sonar token credential you added earlier (e.g., Sonar).

3. Configure Tools:
  - Go to `Jenkins Dashboard > Manage Jenkins > Global Tool Configuration`
  - Click `Add SonarQube Scanner`> Enter `Sonar` as the Name> Check the box for Install automatically and select the latest version.
  - Click `Add Dependency-Check` > Enter `dc` as the Name > Check the box for Install automatically and select the latest version

4. Create a New Pipeline:
  - Go to the `Jenkins Dashboard > New Item`
  - Definition: Select `Pipeline script from SCM.`
  - SCM: Choose `Git`
  - Repository URL: Enter your Git repository URL
  - Branch: Enter `main`
  - Script Path: Enter `Jenkinsfile`(or your custom pipeline script name if different)

### Step 9: Access Website
You can access the web application at:
`http://ec2-public-ip:5173/`

#### Why These Tools?

1. **SonarQube Scan**
SonarQube is essential for continuous code quality checks. It performs static analysis of your codebase, identifying bugs, vulnerabilities, and code smells. It also promotes best practices and coding standards across your team.

2. **SonarQube Quality Gates**
Quality Gates in SonarQube act as checkpoints. They define criteria for code quality, ensuring that your code meets specific thresholds before being deployed. This ensures that your production code is always of high quality.

3. **OWASP Dependency-Check**
The OWASP Dependency-Check plugin in Jenkins helps to identify known vulnerabilities in your project dependencies. By analyzing the libraries used in your project, it detects security flaws and helps you ensure that you're not deploying insecure software.

4. **Trivy**
Trivy is a security scanner that helps to detect vulnerabilities in your container images. By integrating Trivy into your CI/CD pipeline, you can ensure that no security risks are introduced during containerization.

