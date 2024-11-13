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
   - [Additional Hardening Measures](#additional-hardening-measures)
   - [Advanced User and Group Management](#advanced-user-and-group-management)
   - [System Recovery Preparations](#system-recovery-preparations)
   - [Networking and Additional Services](#networking-and-additional-services)
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
   - Create at least two encrypted partitions using LVM (Logical Volume Management).
   
2. **Hostname**:
   - Set your hostname to your login followed by "42" (e.g., `yourlogin42`).
   - You will be asked to change this during your evaluation.

3. **SSH Service**:
   - Configure SSH to run on port `4242`.
   - Ensure root login is disabled for SSH connections.

4. **Firewall Configuration**:
   - Use `ufw` for Debian or `firewalld` for Rocky Linux.
   - Open only port `4242` and ensure the firewall is active on startup.

---

### User and Security Policies

1. **User Account Setup**:
   - Create a user with your login name, adding them to `user42` and `sudo` groups.

2. **Password Policy**:
   - Password expiration every 30 days.
   - Minimum 2 days required before password changes.
   - Passwords should include a mix of uppercase, lowercase, and numeric characters, and should be at least 10 characters long.
   - Warning issued 7 days before password expiration.

3. **Sudo Configuration**:
   - Limit failed `sudo` attempts to 3.
   - Custom error message for incorrect password attempts.
   - Log all `sudo` actions to `/var/log/sudo/`.
   - Enable TTY mode and restrict `sudo` path to `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`.

---

### Monitoring Script

Create a script named `monitoring.sh` in Bash that outputs key system metrics every 10 minutes, using `wall` for broadcasting. The script should display:

- System architecture and kernel version.
- Physical and virtual processor count.
- Available RAM and its usage percentage.
- Disk space usage percentage.
- CPU usage percentage.
- Date and time of the last reboot.
- LVM status.
- Active connections.
- User count.
- Server IPv4 and MAC addresses.
- Total number of commands run with `sudo`.

*Tip*: Use `cron` to automate this script.

---

## Bonus Objectives

### Additional Hardening Measures

1. **Intrusion Detection**:
   - Install and configure `fail2ban` to prevent brute-force attacks.

2. **Enhanced Logging**:
   - Use `rsyslog` or similar for centralized logging of system events and user actions.

3. **Resource Limits**:
   - Apply resource limits per session using `ulimit` to control CPU and memory usage.

---

### Advanced User and Group Management

1. **Automatic Account Lock**:
   - Lock accounts after multiple failed login attempts.

2. **Advanced Sudo Policies**:
   - Define specific `sudo` permissions for critical commands using detailed entries in the `sudoers` file.

---

### System Recovery Preparations

1. **Automated Backups**:
   - Use `cron` to automate backups of critical data and system configurations.
   
2. **Snapshot Management**:
   - Set up automated snapshots for quick rollback in case of failure.

---

### Networking and Additional Services

1. **Network Time Protocol (NTP)**:
   - Use `ntpd` or `chrony` to synchronize server time.

2. **Advanced Firewall Rules**:
   - Configure additional firewall rules, considering VPN tunneling for secure access.

---

## Testing and Documentation

- **Testing**: Verify each configuration change to ensure connectivity, permissions, and access restrictions work as expected.
- **Documentation**: Document every step and setting, including configurations, commands, and reasoning. This will assist during evaluations.

---

### Additional Resources

- [Debian Documentation](https://www.debian.org/doc/)
- [Rocky Linux Documentation](https://docs.rockylinux.org/)
- [VirtualBox User Manual](https://www.virtualbox.org/manual/UserManual.html)

Good luck with the **Born2beRoot** project! Follow this roadmap carefully to secure a successful evaluation and gain comprehensive system administration skills.
