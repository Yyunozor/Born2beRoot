# **Born2beRoot Cheat Sheet**

## **1. Preliminary Setup**

- **Repository Check**:
    - Ensure the repository contains `signature.txt` with the correct VM signature:
        
        ```bash
        sha1sum <vm>.vdi
        
        ```
GUI ? >> systemctl get-default

    - Compare the result with the content of `signature.txt`.
- **Launch VM**:
    - Verify that the VM boots correctly and **no GUI** is installed.
    - Ensure all required services (e.g., SSH, UFW) are running.

---

## **2. Mandatory Configuration**

### **2.1 General Configuration**

- **Hostname**:
    - Display the hostname:
        
        ```bash
        hostname
        
        ```
        
    - Change the hostname:
        
        ```bash
        sudo hostnamectl set-hostname <new_hostname>
        
        ```
        
    - Ensure persistence after reboot.
- **Partitioning**:
    - View disk partitions:
        
        ```bash
        lsblk
        
        ```
        
    - Verify LVM (Logical Volume Manager):
        
        ```bash
        lvdisplay
        
        ```
        
    - **Explanation**: LVM allows dynamic resizing and efficient storage management.

---

### **2.2 Users and Groups**

- **Default User**:
    - Check user information:
        
        ```bash
        getent passwd <username>
        
        ```
        
    - Ensure the user belongs to `sudo` and `user42` groups:
        
        ```bash
        groups <username>
        
        ```
        
- **Create a New User**:
    
    ```bash
    sudo adduser <new_user>
    
    ```
    
- **Password Policy**:
    - Set password policies in `/etc/login.defs` and `/etc/security/pwquality.conf`.
    - Rules:
        - Expire passwords every 30 days.
        - Minimum age: 2 days.
        - Warn users 7 days before expiration.
        - Require at least 10 characters (uppercase, lowercase, numbers).
- **Groups**:
    - Create a new group:
        
        ```bash
        sudo groupadd evaluating
        
        ```
        
    - Add the user to the group:
        
        ```bash
        sudo usermod -aG evaluating <new_user>
        
        ```
        

---

### **2.3 Sudo Configuration**

- **Check Sudo Installation**:
    
    ```bash
    sudo -V
    
    ```
    
- **Sudo Logs**:
    - Ensure `/var/log/sudo/` exists:
        
        ```bash
        ls /var/log/sudo/
        
        ```
        
    - View logs:
        
        ```bash
        sudo cat /var/log/sudo/sudo.log
        
        ```
        
- **Sudo Rules**:
    - Limit authentication retries:
        
        ```makefile
        passwd_tries=3
        
        ```
        
    - Log input/output:
        
        ```
        log_input, log_output
        
        ```
        
    - Display a custom error message:
        
        ```makefile
        badpass_message="Custom error message"
        
        ```
        
    - Restrict sudo paths:
        
        ```makefile
        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
        
        ```
        

---

### **2.4 Firewall (UFW or Firewalld)**

- **Check UFW**:
    
    ```bash
    sudo ufw status
    
    ```
    
- **Rules**:
    - Allow port 4242:
        
        ```bash
        sudo ufw allow 4242
        sudo ufw reload
        
        ```
        
    - Add and remove rules:
        
        ```bash
        sudo ufw allow <port>
        sudo ufw delete allow <port>
        
        ```
        

---

### **2.5 SSH Configuration**

- **Check SSH Status**:
    
    ```bash
    sudo systemctl status ssh
    
    ```
    
- **Verify Configuration**:
    - Ensure SSH uses port 4242:
        
        ```bash
        sudo cat /etc/ssh/sshd_config | grep Port
        
        ```
        
    - Disable root login:
        
        ```bash
        sudo cat /etc/ssh/sshd_config | grep PermitRootLogin
        
        ```
        
- **Test SSH**:
    - Connect as a user:
        
        ```bash
        ssh <username>@<ip_address> -p 4242
        
        ```
        

---

### **2.6 Monitoring Script**

- **Purpose**: Displays system stats every 10 minutes via `cron`.
- **Key Information Displayed**:
    - Architecture, CPU info, memory usage, disk usage, CPU load, last boot, active connections, and more.
- **Important Commands**:
    
    ```bash
    uname -a        # Architecture
    free -m         # Memory usage
    df -h           # Disk usage
    vmstat          # CPU load
    ss -ta          # TCP connections
    who -b          # Last boot
    hostname -I     # IP address
    ip link         # MAC address
    
    ```
    
- **Verify Cron Job**:
    
    ```bash
    crontab -l
    
    ```
    

---

## **3. Useful Commands**

- **List Users**:
    
    ```bash
    getent passwd
    
    ```
    
- **List Groups**:
    
    ```bash
    getent group
    
    ```
    
- **Show Open Ports**:
    
    ```bash
    sudo ss -tuln
    
    ```
    
- **Display Running Services**:
    
    ```bash
    sudo systemctl list-units --type=service
    
    ```
    

---

## **4. Key Explanations**

- **getent**: Queries system databases (e.g., `passwd`, `group`).
- **LVM**: Logical Volume Manager allows flexible disk partition management.
- **UFW**: Firewall tool for filtering incoming/outgoing network traffic.
- **Sudo Logs**: Tracks commands run with `sudo` for accountability.
- **Monitoring**: Identifies potential system issues early (e.g., high CPU usage).

---

# **Lighttpd Cheat Sheet**

## **1. What is Lighttpd?**

- A lightweight, fast, and resource-efficient web server.
- Ideal for static websites, small dynamic web apps, or limited-resource environments.

---

## **2. Installation**

### **Install Lighttpd**:

```bash
sudo apt update
sudo apt install lighttpd -y

```

### **Enable and Start the Service**:

```bash
sudo systemctl enable lighttpd
sudo systemctl start lighttpd

```

### **Verify Installation**:

- Check service status:
    
    ```bash
    sudo systemctl status lighttpd
    
    ```
    
- Access the default page in your browser:
    - `http://<your_server_ip>`

---

## **3. Configuration**

### **Edit Main Configuration File**:

- Open the file:
    
    ```bash
    sudo nano /etc/lighttpd/lighttpd.conf
    
    ```
    

### **Common Configurations**:

- **Change Server Port**:
    
    ```
    server.port = 8080
    
    ```
    
- **Set Document Root**:
    
    ```
    server.document-root = "/var/www/html"
    
    ```
    
- **Enable HTTPS (if SSL is configured)**:
    
    ```
    $SERVER["socket"] == ":443" {
        ssl.engine = "enable"
        ssl.pemfile = "/path/to/certificate.pem"
    }
    
    ```
    

---

## **4. Serving Content**

### **Serve Custom Content**:

- Place files in the document root:Example content:
    
    ```bash
    sudo nano /var/www/html/index.html
    
    ```
    
    ```html
    <html>
    <body><h1>Welcome to Lighttpd!</h1></body>
    </html>
    
    ```
    

### **Restart Lighttpd**:

```bash
sudo systemctl restart lighttpd

```

---

## **5. Logs**

- **Access Log**:
    
    ```bash
    sudo tail -f /var/log/lighttpd/access.log
    
    ```
    
- **Error Log**:
    
    ```bash
    sudo tail -f /var/log/lighttpd/error.log
    
    ```
    

---

## **6. Modules**

### **Enable Modules**:

- Enable a module:Example:
    
    ```bash
    sudo lighty-enable-mod <module_name>
    
    ```
    
    ```bash
    sudo lighty-enable-mod rewrite
    
    ```
    
- Restart Lighttpd:
    
    ```bash
    sudo systemctl restart lighttpd
    
    ```
    

### **List Available Modules**:

```bash
ls /usr/share/lighttpd

```

---

## **7. Firewall**

- Open the Lighttpd port (default: 80, or custom like 8080):
    
    ```bash
    sudo ufw allow 80
    sudo ufw allow 8080
    sudo ufw reload
    
    ```
    

---

## **8. Key Commands**

| **Command** | **Purpose** |
| --- | --- |
| `sudo systemctl status lighttpd` | Check service status |
| `sudo systemctl start lighttpd` | Start Lighttpd |
| `sudo systemctl stop lighttpd` | Stop Lighttpd |
| `sudo systemctl restart lighttpd` | Restart Lighttpd |
| `sudo systemctl enable lighttpd` | Enable Lighttpd at startup |

---

# **Fail2ban Setup Cheat Sheet**

## **What is Fail2ban?**

Fail2ban monitors log files for suspicious activity (e.g., failed login attempts) and bans malicious IPs to protect your server.

---

## **Setup Steps**

1. **Install Fail2ban**:
    
    ```bash
    sudo apt update
    sudo apt install fail2ban -y
    
    ```
    
2. **Enable and Start the Service**:
    
    ```bash
    sudo systemctl enable fail2ban
    sudo systemctl start fail2ban
    
    ```
    
3. **Copy the Configuration File**:
    
    ```bash
    sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
    
    ```
    
4. **Edit `jail.local`**:
    - Open the file:
        
        ```bash
        sudo nano /etc/fail2ban/jail.local
        
        ```
        
    - Enable SSH protection:
        
        ```
        sshd]
        enabled = true
        port = 4242
        maxretry = 3
        bantime = 3600
        
        ```
        
5. **Restart Fail2ban**:
    
    ```bash
    sudo systemctl restart fail2ban
    
    ```
    

---

## **Commands for Monitoring**

1. **Check Status**:
    
    ```bash
    sudo fail2ban-client status
    
    ```
    
2. **View Specific Jail (e.g., SSH)**:
    
    ```bash
    sudo fail2ban-client status sshd
    
    ```
    
3. **Unban an IP**:
    
    ```bash
    sudo fail2ban-client set sshd unbanip <ip_address>
    
    ```
    
4. **View Logs**:
    
    ```bash
    sudo tail -f /var/log/fail2ban.log
    
    ```
    

---

## **How it Works**

- **What it Does**: Bans IPs with too many failed login attempts (default: SSH).
- **Customizable**: You can adjust `bantime`, `maxretry`, and monitored services.
- **Example**: After 3 failed SSH logins on port 4242, the IP will be banned for 1 hour.

---
