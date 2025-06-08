sudo apt-get install docker.io -y
sudo apt-get install docker-compose -y
whoami
ubuntu
ubuntu@ip-172-30-21-254:~$ sudo usermod -aG docker ubuntu
ubuntu@ip-172-30-21-254:~$ sudo reboot

sudo apt update
sudo apt install fontconfig openjdk-21-jre

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

systemctl status jenkins
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

docker run -itd --name sonarqube-server -p 9000:9000 sonarqube:lts-community

sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy

jenkins plugins
SonarQube Scanner
Sonar Quality Gates
OWASP Dependency-Check
docker

sudo usermod -aG docker jenkins

sudo systemctl restart docker 
sudo systemctl restart jenkins 