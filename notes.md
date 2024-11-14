# Born2BeRoot VM Setup Guide

This guide provides a step-by-step process to initialize, configure, and secure your virtual machine for the Born2BeRoot project. Follow these steps carefully to ensure a successful setup.

## Table of Contents
1. [Basic System Initialization](#1-basic-system-initialization)
2. [Sudo Installation and Configuration](#2-sudo-installation-and-configuration)
3. [SSH Installation and Configuration](#3-ssh-installation-and-configuration)
4. [Firewall Configuration (UFW)](#4-firewall-configuration-ufw)
5. [SSH Remote Access Setup](#5-ssh-remote-access-setup)
6. [Password Policy Configuration](#6-password-policy-configuration)
7. [User and Group Management](#7-user-and-group-management)
8. [Sudo Log Setup](#8-sudo-log-setup)
9. [Monitoring Script Setup](#9-monitoring-script-setup)
10. [Signature Creation](#10-signature-creation)

---

## 1. Basic System Initialization

### Login as User & View Partitions (`lsblk`):
- Use `lsblk` to view the partitions, ensuring **LVM** and partition configuration are correct.

---

## 2. Sudo Installation and Configuration

### Install Sudo:
- **Become the Root User**: Run `su -` to switch to the root user.
- **Update the System**:
  ```bash
  apt-get update -y && apt-get upgrade -y
  ```
- **Install Sudo**:
  ```bash
  apt install sudo
  ```

### Add User to Sudo Group:
- Run the following command to add your user to the **sudo** group:
  ```bash
  usermod -aG sudo your_username
  ```
  This allows the user to execute commands as a superuser.

### Configure Sudoers File:
- Open the sudo configuration file using `visudo`:
  ```bash
  sudo visudo
  ```
  Add specific user privileges as needed.

---

## 3. SSH Installation and Configuration

### Install OpenSSH Server:
- Install SSH to allow remote access:
  ```bash
  sudo apt install openssh-server
  ```

### Configure SSH Port:
- Edit the SSH configuration file to change the default port from **22** to **4242**:
  ```bash
  sudo vim /etc/ssh/sshd_config
  ```
  Find the line `#Port 22`, change it to `Port 4242`, and remove the `#`.

### Restart SSH:
- Apply the new configuration:
  ```bash
  sudo service ssh restart
  ```

---

## 4. Firewall Configuration (UFW)

### Install UFW and Enable Rules:
- **Install UFW**:
  ```bash
  apt-get install ufw
  ```
- **Enable UFW**:
  ```bash
  sudo ufw enable
  ```
- **Allow SSH and Port 4242**:
  ```bash
  sudo ufw allow ssh
  sudo ufw allow 4242
  ```

---

## 5. SSH Remote Access Setup

### Port Forwarding in VirtualBox:
- Set the network adapter's **Port Forwarding** rules in VirtualBox to ensure access to port **4242** from your host machine.

### Connect via SSH:
- Use the following command to connect to your VM:
  ```bash
  ssh your_username@127.0.0.1 -p 4242
  ```

---

## 6. Password Policy Configuration

### Install PAM Quality Library:
- Install the **Password Quality Checking Library**:
  ```bash
  sudo apt-get install libpam-pwquality
  ```

### Edit Password Rules:
- Configure the file `/etc/pam.d/common-password` to set rules like **minimum length**, **character types**, and **preventing simple passwords**.

### Password Expiry Configuration:
- Edit `/etc/login.defs` to set:
  ```
  PASS_MAX_DAYS 30
  PASS_MIN_DAYS 2
  PASS_WARN_AGE 7
  ```
  This enforces password expiration policies.

---

## 7. User and Group Management

### Create Groups:
- Use the following command to create a group named **user42**:
  ```bash
  sudo groupadd user42
  ```

### Create and Add Users to Groups:
- **Create a New User**:
  ```bash
  sudo adduser new_username
  ```
- **Add User to Group**:
  ```bash
  sudo usermod -aG user42 your_username
  ```

---

## 8. Sudo Log Setup

### Create Log Directory:
- Navigate to `/var/log` and create a directory for logging **sudo** commands:
  ```bash
  cd /var/log
  mkdir sudo
  cd sudo
  touch sudo.log
  ```

### Configure Sudoers to Log:
- Edit `/etc/sudoers` to add logging directives:
  ```
  Defaults logfile="/var/log/sudo/sudo.log"
  ```

---

## 9. Monitoring Script Setup

### Create `monitoring.sh` Script:
- Create a script to output system stats like **CPU load**, **memory usage**, and **disk space**:
  ```bash
  cd /usr/local/bin/
  touch monitoring.sh
  chmod 777 monitoring.sh
  ```

### Automate with Cron:
- Use `crontab` to schedule the script every 10 minutes:
  ```bash
  sudo crontab -u root -e
  ```
  Add the line:
  ```
  */10 * * * * /usr/local/bin/monitoring.sh
  ```

---

## 10. Signature Creation

### Create Signature for VM Disk:
- Navigate to the folder containing your `.vdi` file and run the following command to generate a signature for submission:
  ```bash
  shasum VirtualBox.vdi
  ```

---

By following these steps, you will have successfully set up, configured, and secured your virtual machine for the Born2BeRoot project. Make sure to test each configuration thoroughly to ensure that everything works as expected.
