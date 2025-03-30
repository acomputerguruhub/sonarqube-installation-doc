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
