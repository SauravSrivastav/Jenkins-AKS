https://blog.jongallant.com/2017/04/jenkins-on-azure/

Use Cloud Shell or Putty locally to login into the machine:

ssh TestVM@52.170.215.27

# Step1: Install Java
sudo apt-get update
sudo apt-get install openjdk-8-jdk
# sudo apt-get install openjdk-8-jre

# Step2: Install Jenkins
vi install_jenkins.sh
chmod +x install_jenkins.sh

https://www.jenkins.io/doc/book/installing/#debianubuntu

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins

# Step3: Now we need to start the Jenkins Services:

sudo systemctl start jenkins 
sudo systemctl status jenkins 

# Note: Jenkins listens on port 8080 so we need to go to the portal and enable(TCP)

# Step4: Unlock Jenkins

/var/lib/jenkins/secrets/initialAdminPassword
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
# 957a3373ebd748dc8ac98726e202ea81

