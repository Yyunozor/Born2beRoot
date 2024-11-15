# Born2beRoot Project Roadmap

Welcome to the **Born2beRoot** project! This roadmap is designed to help you systematically complete both mandatory and bonus parts of this system administration project. You’ll gain hands-on experience in virtualization, server security, and automation.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Mandatory Part](#mandatory-part)
   - [System Setup and Configuration](#system-setup-and-configuration)
   - [User and Security Policies](#user-and-security-policies)
   - [Monitoring Script](#monitoring-script)
3. [Bonus Objectives](#bonus-objectives)
   - [Partition Setup](#partition-setup)
   - [WordPress Setup](#wordpress-setup)
   - [Additional Service Setup](#additional-service-setup)
4. [Testing and Documentation](#testing-and-documentation)

---

## Project Overview

The **Born2beRoot** project introduces you to essential system administration tasks. You will set up a virtual machine using [VirtualBox](https://www.virtualbox.org/) or [UTM](https://mac.getutm.app/) and configure it according to specific security and performance standards.

- **Virtualization Tool**: VirtualBox (or UTM if VirtualBox is unavailable).
- **Operating System**: Latest stable version of Debian or Rocky Linux.
  - **Note**: Rocky Linux requires SELinux, while Debian uses AppArmor.

---

## Mandatory Part

### System Setup and Configuration

1. **Disk Partitioning**:
   - Use Logical Volume Management (**LVM**) to create at least **two encrypted partitions**. LVM allows for easier disk management and scaling, making it essential for setting up encrypted partitions.
   - Create the following partitions:
     - **Root Partition (/)**: This will contain all the essential system files.
     - **Home Partition (/home)**: Store user-specific files in a separate partition to ensure data security and easier backups.
     - **Swap Partition**: A swap partition is recommended to assist with memory management, especially if the system runs low on RAM.
   - **Partition Size Recommendations**:
     - Root: At least **10-15 GB**.
     - Home: Allocate as much space as possible for user data.
     - Swap: Typically **1-2 times** the size of your RAM, depending on available disk space and performance requirements.

2. **Hostname**:
   - Set the hostname of your virtual machine to your login followed by "42" (e.g., `yourlogin42`).
   - You will be asked to change this during your evaluation.
   - **Verify Hostname**: Use the command `hostnamectl` to verify or modify the hostname during the evaluation.

3. **Disable Graphical User Interface**:
   - Install the system in command-line mode only; installing a graphical interface like **X.org** is **prohibited**. This restriction helps you focus on command-line tools and best practices for server management.

4. **SSH Service**:
   - Configure SSH to run on port **4242** instead of the default port 22. This non-standard port helps to reduce automated attack attempts.
   - **Root Access**: Disable SSH access for the root user by editing `/etc/ssh/sshd_config` and setting `PermitRootLogin no`.
   - **SSH Key Pair**: Generate an SSH key pair (`ssh-keygen`) and use it for authentication. Practice adding the public key to `/home/username/.ssh/authorized_keys` for secure access without passwords.
   - **SSH Hardening**: Additional SSH hardening steps include disabling password-based authentication (`PasswordAuthentication no`), enabling `AllowUsers username` to limit access, and configuring idle timeout (`ClientAliveInterval 300` and `ClientAliveCountMax 2`).

5. **Firewall Configuration**:
   - Use **UFW** (for Debian) or **firewalld** (for Rocky Linux).
   - **Allow Only Port 4242**: Ensure that only **port 4242** is open. Use commands like `ufw allow 4242/tcp` to set this rule, and `ufw enable` to activate the firewall.
   - **Logging**: Enable firewall logging to monitor traffic attempts (`ufw logging on`). This can help identify any unauthorized access attempts.

---

### User and Security Policies

1. **User Account Setup**:
   - Create a user with your login name, adding them to `user42` and `sudo` groups.
   - **Verify Group Membership**: Use the command `groups username` to verify that the user belongs to the correct groups.

2. **Password Policy**:
   - **Password Length**: Set a minimum password length of **10 characters** by editing `/etc/login.defs`.
   - **Complexity Requirements**: Passwords must contain an uppercase letter, a lowercase letter, and a number. Modify `/etc/pam.d/common-password` to enforce complexity requirements.
   - **Expiration**: Set passwords to expire every **30 days** by using the `chage` command: `chage -M 30 username`.
   - **Modification Restriction**: Prevent password modifications within **2 days** after setting by running `chage -m 2 username`.
   - **Warning Before Expiration**: Set a warning **7 days** before password expiration using `chage -W 7 username`.
   - **Password History**: Prevent users from reusing old passwords by modifying `/etc/pam.d/common-password` and adding `remember=5` to keep a history of the last 5 passwords.
   - **Lock Account after Failed Attempts**: Use `pam_tally2` to lock user accounts after a specific number of failed login attempts (e.g., 5 attempts).

3. **Sudo Configuration**:
   - **Authentication Attempts**: Limit `sudo` authentication attempts to **3** by editing `/etc/sudoers` with `visudo` and adding `Defaults passwd_tries=3`.
   - **Custom Error Message**: Set a custom error message for incorrect password attempts by adding `Defaults badpass_message="Custom error message"` to `/etc/sudoers`.
   - **Log Sudo Commands**: Enable logging of all `sudo` commands by editing `/etc/sudoers` and adding `Defaults logfile="/var/log/sudo/sudo.log"`.
   - **TTY Mode**: Ensure that `sudo` commands can only be run in TTY mode for added security. Add `Defaults requiretty` to `/etc/sudoers`.
   - **Restricted Paths**: Restrict the commands that can be run with `sudo` by setting `Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"`.

---

### Monitoring Script

Create a script named `monitoring.sh` in Bash that outputs key system metrics every 10 minutes, using `wall` for broadcasting. The script should display:

- **System Architecture and Kernel Version**: Use `uname -a` to get the architecture and kernel details.
- **Number of Physical and Virtual Processors**: Use `lscpu | grep "^CPU(s):"` to find the number of CPUs.
- **Available RAM and Usage Percentage**: Use `free -m` and calculate the percentage usage.
- **Disk Space Usage Percentage**: Use `df -h` to display the disk usage.
- **CPU Usage Percentage**: Use `top -bn1 | grep "Cpu(s)"` to get CPU utilization.
- **Date and Time of Last Reboot**: Use `who -b` to find the last reboot time.
- **LVM Status**: Use `lsblk` to check if LVM is active.
- **Number of Active Connections**: Use `ss -s` to count active connections.
- **Number of Users Logged In**: Use `who | wc -l` to find out how many users are logged in.
- **IPv4 Address and MAC Address**: Use `ip a` to find IP and MAC details.
- **Total Number of Commands Run Using Sudo**: Use `grep -c COMMAND /var/log/sudo/sudo.log` to count `sudo` commands executed.
- **Disk I/O Utilization**: Use `iostat` to provide information on disk read/write statistics.

*Tip*: Use `cron` to automate this script.

---

## Bonus Objectives

### Partition Setup

- Set up the disk partitions to achieve a structure similar to the one specified in the guidelines. Use LVM to ensure flexibility and encryption for increased security.

### WordPress Setup

- Set up a functional WordPress website using **lighttpd**, **MariaDB**, and **PHP**.
  - **Lighttpd Setup**: Install and configure **lighttpd** by editing `/etc/lighttpd/lighttpd.conf`. Ensure the server listens on a custom port to avoid conflicts.
  - **Database Setup**: Install **MariaDB** and create a database for WordPress. Secure the database using `mysql_secure_installation`.
  - **PHP Configuration**: Install **PHP** and necessary modules (`php-mysql`, `php-cgi`). Configure **lighttpd** to process PHP by enabling the `mod_fastcgi` module.
  - **Secure WordPress**: Harden the WordPress installation by using strong passwords, limiting login attempts, and setting correct file permissions (`chmod 755 wp-content`).

### Additional Service Setup

- Set up a service of your choice (excluding **NGINX** or **Apache2**).
  - **Justification**: Be prepared to justify why this service is useful for a server environment.
  - **Service Hardening**: Ensure that the service is securely configured, including proper user permissions, firewall rules, and logging.
- You may open more ports to suit your needs for the additional services. Adapt **UFW/Firewalld** rules accordingly.

*Note*: The bonus part will only be assessed if the mandatory part is **perfect**—meaning all mandatory requirements are fully met and function correctly.

---

## Testing and Documentation

- **Testing**: Verify each configuration change to ensure connectivity, permissions, and access restrictions work as expected.
- **Documentation**: Document every step and setting, including configurations, commands, and reasoning. This will assist during evaluations.

---

### Additional Resources

- [Debian Documentation](https://www.debian.org/doc/)
- [Rocky Linux Documentation](https://docs.rockylinux.org/)
- [VirtualBox User Manual](https://www.virtualbox.org/manual/UserManual.html)
- [42 Cursus Born2beRoot Guide](https://42-cursus.gitbook.io/guide/rank-01/born2beroot)
- [Born2BeRoot Guide by pasqualerossi](https://github.com/pasqualerossi/Born2BeRoot-Guide)
- [Born2BeRoot Guide by mathieu-soysal](https://mathieu-soysal.gitbook.io/born2beroot/installing-debian/non-bonus-partition-disks)
- [Born2BeRoot Guide by gemartin99](https://github.com/gemartin99/Born2beroot-Tutorial/blob/main/README_EN.md)

---

## Conclusion

Born2BeRoot is a challenging project that will strengthen your skills in Linux system administration, virtualization, and security. By following this detailed roadmap, you will be well-prepared to complete both the mandatory and bonus parts of the project successfully.
