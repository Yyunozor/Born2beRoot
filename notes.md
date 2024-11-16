# Born2BeRoot VM Setup Guide - Advanced Reference

This guide provides a step-by-step process to initialize, configure, and secure your virtual machine for the Born2BeRoot project, along with detailed explanations of all command lines, arguments, and the underlying concepts. This expanded guide aims to prepare you for even the most fastidious and demanding evaluator.

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
- **Command**: `lsblk`
  - **Description**: Lists information about all available block devices (disks and partitions).
  - **Purpose**: To verify that partitions have been created correctly and that **LVM** is set up as expected.
  - **Common Flags**: 
    - `-f`: Shows the filesystem type.
    - `-o`: Customizes the output columns (e.g., `lsblk -o NAME,SIZE,TYPE,MOUNTPOINT`).
  
---

## 2. Sudo Installation and Configuration

### Install Sudo:
- **Become the Root User**: Run `su -`
  - **`su -`**: Switches to the root user. The `-` option starts a login shell, loading root's environment variables.

- **Update the System**:
  ```bash
  apt-get update -y && apt-get upgrade -y
  ```
  - **`apt-get update`**: Updates the list of available packages and their versions.
  - **`-y`**: Automatically answers "yes" to prompts.
  - **`apt-get upgrade`**: Installs the latest versions of all packages currently installed on the system.

- **Install Sudo**:
  ```bash
  apt install sudo
  ```
  - **`apt install`**: Installs a package.
  - **`sudo`**: A utility that allows permitted users to execute commands as the root user or another user.

### Add User to Sudo Group:
- **Command**: `usermod -aG sudo your_username`
  - **`usermod`**: Modifies a user account.
  - **`-aG`**: Adds the user to the specified group without removing them from other groups (`-a` means append).
  - **Purpose**: Adding your user to the `sudo` group grants the user administrative privileges.

### Configure Sudoers File:
- **Command**: `sudo visudo`
  - **`visudo`**: Opens the `/etc/sudoers` file safely, ensuring proper syntax before saving.
  - **Editing**: Add a line like `your_username    ALL=(ALL:ALL) ALL` to grant full administrative privileges.
  - **Explanation**: This line allows `your_username` to execute any command as any user or group.

---

## 3. SSH Installation and Configuration

### Install OpenSSH Server:
- **Command**: `sudo apt install openssh-server`
  - **`openssh-server`**: Installs the SSH server, allowing remote login via SSH.
  - **Purpose**: To allow secure remote access to the VM.

### Configure SSH Port:
- **Command**: `sudo vim /etc/ssh/sshd_config`
  - **`vim`**: A text editor used to modify files.
  - **Edit**: Change the line `#Port 22` to `Port 4242` to set SSH to listen on port 4242.
  - **Reason**: Changing the default port reduces exposure to automated attacks that target the default SSH port (22).

### Restart SSH:
- **Command**: `sudo service ssh restart`
  - **`service`**: Manages services on the system.
  - **`restart`**: Restarts the SSH service to apply the new configuration.

---

## 4. Firewall Configuration (UFW)

### Install UFW and Enable Rules:
- **Install UFW**:
  ```bash
  apt-get install ufw
  ```
  - **`ufw`**: Uncomplicated Firewall, a frontend for `iptables` to simplify firewall management.

- **Enable UFW**:
  ```bash
  sudo ufw enable
  ```
  - **Purpose**: Activates the firewall to start filtering traffic based on configured rules.

- **Allow SSH and Port 4242**:
  ```bash
  sudo ufw allow ssh
  sudo ufw allow 4242
  ```
  - **`allow ssh`**: Opens the default SSH port (22).
  - **`allow 4242`**: Opens the custom SSH port (4242) for remote access.

---

## 5. SSH Remote Access Setup

### Port Forwarding in VirtualBox:
- **Purpose**: Set port forwarding in VirtualBox to forward traffic from the host to the VM’s SSH port.
  - **Steps**: Go to **Settings** > **Network** > **Adapter 1** > **Advanced** > **Port Forwarding**.
  - **Add Rule**: Set **Host Port** and **Guest Port** to **4242**.

### Connect via SSH:
- **Command**: `ssh your_username@127.0.0.1 -p 4242`
  - **`ssh`**: Secure Shell, used to connect to remote machines.
  - **`-p 4242`**: Specifies the custom port number (4242).
  - **IP Address**: `127.0.0.1` is the loopback address, referring to the local machine.

---

## 6. Password Policy Configuration

### Install PAM Quality Library:
- **Command**: `sudo apt-get install libpam-pwquality`
  - **`libpam-pwquality`**: Enforces password complexity requirements to improve security.

### Edit Password Rules:
- **File**: `/etc/pam.d/common-password`
  - **Configuration**: Add rules such as `minlen=10 ucredit=-1 lcredit=-1 dcredit=-1` to enforce password length and complexity.

### Password Expiry Configuration:
- **File**: `/etc/login.defs`
  - **Edit**:
  ```
  PASS_MAX_DAYS 30
  PASS_MIN_DAYS 2
  PASS_WARN_AGE 7
  ```
  - **Explanation**:
    - **`PASS_MAX_DAYS`**: Maximum number of days a password is valid.
    - **`PASS_MIN_DAYS`**: Minimum number of days between password changes.
    - **`PASS_WARN_AGE`**: Number of days before password expiration that a warning is given.

---

## 7. User and Group Management

### Create Groups:
- **Command**: `sudo groupadd user42`
  - **`groupadd`**: Creates a new group.
  - **Purpose**: To organize users and assign permissions based on group membership.

### Create and Add Users to Groups:
- **Create a New User**:
  ```bash
  sudo adduser new_username
  ```
  - **`adduser`**: Creates a new user account and sets up the home directory.

- **Add User to Group**:
  ```bash
  sudo usermod -aG user42 your_username
  ```
  - **`usermod -aG`**: Adds the user to a group without affecting existing group memberships.

---

## 8. Sudo Log Setup

### Create Log Directory:
- **Commands**:
  ```bash
  cd /var/log
  mkdir sudo
  cd sudo
  touch sudo.log
  ```
  - **Purpose**: Set up a dedicated log file for `sudo` commands for auditing purposes.

### Configure Sudoers to Log:
- **Edit `/etc/sudoers`** to add:
  ```
  Defaults logfile="/var/log/sudo/sudo.log"
  ```
  - **Purpose**: Logs all `sudo` command usage to `/var/log/sudo/sudo.log` for tracking administrative actions.

---

## 9. Monitoring Script Setup

### Create `monitoring.sh` Script:
- **Commands**:
  ```bash
  cd /usr/local/bin/
  touch monitoring.sh
  chmod 755 monitoring.sh
  ```
  - **`chmod 755`**: Grants read, write, and execute permissions to the owner, and read and execute permissions to others. This is more secure than `777`.

- **Script Contents**:
  - The script should gather system metrics such as **CPU load**, **memory usage**, **disk space**, etc. Example script:
    ```bash
    #!/bin/bash
    arc=$(uname -a)
    pcpu=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l)
    vcpu=$(grep "^processor" /proc/cpuinfo | wc -l)
    fram=$(free -m | awk '$1 == "Mem:" {print $2}')
    uram=$(free -m | awk '$1 == "Mem:" {print $3}')
    pram=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')
    fdisk=$(df -BG | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')
    udisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')
    pdisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}')
    cpul=$(top -bn1 | grep "^%Cpu" | cut -c 9- | xargs | awk '{printf("%.1f%%"), $1 + $3}')
    lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')
    lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo no; else echo yes; fi)
    ctcp=$(ss -Ht state established | wc -l)
    ulog=$(users | wc -w)
    ip=$(hostname -I)
    mac=$(ip link show | grep "ether" | awk '{print $2}')
    cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)
    wall "\n#Architecture: $arc\n#CPU physical: $pcpu\n#vCPU: $vcpu\n#Memory Usage: $uram/${fram}MB ($pram%)\n#Disk Usage: $udisk/${fdisk}Gb ($pdisk%)\n#CPU load: $cpul\n#Last boot: $lb\n#LVM use: $lvmu\n#Connections TCP: $ctcp ESTABLISHED\n#User log: $ulog\n#Network: IP $ip ($mac)\n#Sudo: $cmds cmd"
    ```

### Automate with Cron:
- **Command**: `sudo crontab -u root -e`
  - Add the following line to schedule the script every 10 minutes:
    ```
    */10 * * * * /usr/local/bin/monitoring.sh
    ```
  - **Purpose**: Automates the execution of the monitoring script to gather system metrics every 10 minutes.

---

## 10. Signature Creation

### Create Signature for VM Disk:
- **Command**:
  ```bash
  shasum /path/to/VirtualBox.vdi
  ```
  - **Purpose**: Generates a unique signature (hash) for the VM disk file, which can be used to verify its integrity.
  - **Explanation**: The `shasum` command calculates a SHA checksum, which ensures that the disk file has not been tampered with.

---

By following these steps, you will have successfully set up, configured, and secured your virtual machine for the Born2BeRoot project. Make sure to test each configuration thoroughly to ensure that everything works as expected. This guide is designed to not only help you complete the project but also to understand each step in depth, preparing you to answer any questions from a demanding evaluator.
