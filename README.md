# How To Install SonarQube on Ubuntu 20.04LTS
This document will talk about how to install SonarQube on Ubuntu 20.04LTS manually step by step, so that you know what is going on and learn from it.

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
* Locate the latest download URL from [SonarQube official download page](https://www.sonarsource.com/products/sonarqube/downloads/). At the time of writing this document, the download URL was as follows:
