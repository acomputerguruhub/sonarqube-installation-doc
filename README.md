# How To Install SonarQube on Ubuntu 22.04 LTS
This document will cover step by step instructions on how to install SonarQube on Ubuntu 22.04 LTS version.

## 1. Prepare your Ubuntu server
* SSH to your Ubuntu server as a non-root user with sudo access.
* Update your server.
```
sudo apt update && sudo apt upgrade -y
```
## 2. Install OpenJDK 17
* Install OpenJDK 17 version.
```
sudo apt install -y openjdk-17-jdk
```
## 3. Install and Configure PostgreSQL
* Add PostgreSQL repository.
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```
* Add PostgreSQL signing key.
```
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```
* Install PostgreSQL.
```
sudo apt install -y postgresql postgresql-contrib
```
* Start and Enable DB server to start automatically on reboot.
```
sudo systemctl enable postgresql && sudo systemctl start postgresql
```
* Change the default PostgreSQL password.
```
sudo passwd postgres
```
* Switch to the postgres user.
```
su - postgres
```
* Create a user named sonar.
```
createuser sonar
```
* Log into PostgreSQL.
```
psql
```
* Set a password for the sonar user. Use a strong password in place of type_strong_password.
```
ALTER USER sonar WITH ENCRYPTED password 'type_strong_password';
```
* Create SonarQube database and set its owner to sonar.
```
CREATE DATABASE sonarqube OWNER sonar;
```
* Grant all privileges on SonarQube database to the user sonar.
```
GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;
```
* To Exit from PostgreSQL.
```
\q
```
* Return to your non-root sudo user account.
```
exit
```
## 4. Download and Install SonarQube
* Install the zip utility, which is needed to unzip the SonarQube files.
```
sudo apt install -y zip
```
* Locate the latest download URL from [SonarQube official download page](https://www.sonarsource.com/products/sonarqube/downloads/).
```
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.3.0.104237.zip
```
* Unzip the downloaded file.
```
sudo unzip sonarqube-25.3.0.104237.zip
```
* Move the unzipped files to /opt/sonarqube directory
```
sudo mv sonarqube-25.3.0.104237 /opt/sonarqube
```
## 5. Add SonarQube Group and User
* Create a ```sonar``` group.
```
sudo groupadd sonar
```
* Create a ```sonar``` user and set ```/opt/sonarqube``` as the home directory.
```
sudo useradd -d /opt/sonarqube -g sonar sonar
```
* Grant the ```sonar``` user access to the ```/opt/sonarqube``` directory.
```
sudo chown sonar:sonar /opt/sonarqube -R
```
## 6. Configure SonarQube
* Edit the SonarQube configuration file.
```
sudo vi /opt/sonarqube/conf/sonar.properties
```
Step 1: Find the following lines.
```
#sonar.jdbc.username=
#sonar.jdbc.password=
```
Step 2: Uncomment the lines, and add the database user sonar and password my_strong_password you created in Section 3.
```
sonar.jdbc.username=sonar
sonar.jdbc.password=my_strong_password
```
Step 3: add ```sonar.jdbc.url``` line below to the above two lines.
```
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
```
Step 4: Save and exit the file.
* Edit the sonar script file.
```
sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
```
Step 1: About 50 lines down, locate this line.
```
#RUN_AS_USER=
```
Step 2: Uncomment the line and change it to.
```
RUN_AS_USER=sonar
```
Step 3: Save and exit the file.
## 7. Setup Systemd Service
* Create a systemd service file to start SonarQube at system boot.
```
sudo vi /etc/systemd/system/sonar.service
```
Step 1: Paste the following lines to the file.
```
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target
```
Step 2: Save and exit the file.
* Enable the SonarQube service to run at system startup.
```
sudo systemctl enable sonar
```
* Start the SonarQube service.
```
sudo systemctl start sonar
```
* Check the service status.
```
sudo systemctl status sonar
```
## 8. Modify Kernel System Limits
* Edit the sysctl configuration file.
```
sudo vi /etc/sysctl.conf
```
Step 1: Add the following lines.
```
vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```
Step 2: Save and exit the file.
* Reboot the system to apply the changes.
```
sudo reboot
```
## 9. Access SonarQube Web Interface
* Access SonarQube in a web browser at your server's IP address on port 9000. For example:
```
http://localhost:9000
```
* Log in with username admin and password admin. SonarQube will prompt you to change your password.
WARNING! SonarQube ships with a default administrator username and password of admin. This default password is not secure, so you’ll want to update it to something more secure as a good security practice.
