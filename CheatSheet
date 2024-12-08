# **Born2beRoot Cheat Sheet**

## **1. Preliminary Checks**

- **Verify Git Repository**:
    - Check the repository for `signature.txt` and compare the signature with the `.vdi` file:
        
        ```bash
        Copy code
        sha1sum <vm>.vdi
        
        ```
        
- **Boot the VM**:
    - Ensure the VM starts without issues and no graphical interface is installed.
- **Login**:
    - Use a non-root user and confirm password policies:
        - **Expiration**: 30 days.
        - **Minimum age**: 2 days.
        - **Warning**: 7 days before expiration.
        - **Complexity**: At least 10 characters, uppercase, lowercase, and numbers.

---

## **2. Mandatory Configuration**

### **General Configuration**

- **Hostname**:
    - Check hostname:
        
        ```bash
        hostname
        
        ```
        
    - Change hostname:
        
        ```bash
        sudo hostnamectl set-hostname <new_hostname>
        
        ```
        
- **Partitions**:
    - List partitions:
        
        ```bash
        lsblk
        
        ```
        
    - Check LVM usage:
        
        ```bash
        lvdisplay
        
        ```
        
    - **Explain**: Benefits of LVM include dynamic resizing and snapshot capabilities.

---

### **Sudo Configuration**

- **Verify Logging**:
    - Ensure `/var/log/sudo/` exists:
        
        ```bash
        ls /var/log/sudo/
        
        ```
        
    - Check logs dynamically:
        
        ```bash
        sudo cat /var/log/sudo/sudo.log
        
        ```
        
- **Configuration Rules**:
    - Verify the following:
        - **Password attempts**: Limited to 3 (`passwd_tries=3`).
        - **Custom error message**: Displays a personalized message.
        - **Input/Output logging**: Commands and results logged (`log_input`, `log_output`).

---

### **Firewall (UFW/Firewalld)**

- **Check Status**:
    
    ```bash
    sudo ufw status
    
    ```
    
- **Ensure Port 4242 is Open**:
    
    ```bash
    sudo ufw allow 4242
    sudo ufw reload
    
    ```
    
- **Add and Remove Rules**:
    - Add a rule:
        
        ```bash
        sudo ufw allow 8080
        
        ```
        
    - Remove a rule:
        
        ```bash
        sudo ufw delete allow 8080
        
        ```
        

---

### **SSH Configuration**

- **Verify SSH**:
    - Check status:
        
        ```bash
        sudo systemctl status ssh
        
        ```
        
    - Ensure SSH uses port 4242:
        
        ```bash
        sudo cat /etc/ssh/sshd_config | grep Port
        
        ```
        
    - Disable root login:
        
        ```bash
        sudo cat /etc/ssh/sshd_config | grep PermitRootLogin
        
        ```
        
- **Test SSH Connection**:
    
    ```bash
    ssh <username>@<ip_address> -p 4242
    
    ```
    

---

## **3. Monitoring Script**

### **Key Features**

- **Information Displayed**:
    - Architecture, CPU details, memory usage, disk usage, CPU load, last boot time, LVM status, TCP connections, logged-in users, network info, and sudo command count.
- **Runs Every 10 Minutes**:
    - Scheduled using `cron`.

### **Cron Command**

Check the cron jobs:

```bash
crontab -l

```

### **Monitoring Script**

```bash
#!/bin/bash

# Function to format and print output
print_info() {
  printf "%-20s: %s\n" "$1" "$2"
}

# Gather system information
get_architecture() {
  uname -a | sed ':a;N;$!ba;s/\n/\n                    /g'
}

get_cpu_info() {
  physical=$(grep "physical id" /proc/cpuinfo | sort -u | wc -l)
  virtual=$(grep -c "processor" /proc/cpuinfo)
  echo "$physical physical cores, $virtual virtual cores"
}

get_memory_info() {
  free --mega | awk '/^Mem:/ {printf "%d/%dMB (%.2f%%)", $3, $2, $3/$2 * 100}'
}

get_disk_info() {
  df -Bm | awk '$NF!~/boot/ && $NF~"^/" {used+=$3; total+=$2} END {printf "%d/%dGB (%d%%)", used/1024, total/1024, (used/total)*100}'
}

get_cpu_load() {
  idle=$(vmstat 1 2 | tail -1 | awk '{print $15}')
  awk "BEGIN {print 100 - $idle \"%\"}"
}

get_last_boot() {
  who -b | awk '{print $3, $4}'
}

get_lvm_status() {
  [ "$(lsblk | grep -c "lvm")" -gt 0 ] && echo "Yes" || echo "No"
}

get_tcp_connections() {
  ss -ta | grep -c ESTAB
}

get_logged_in_users() {
  users | wc -w
}

get_network_info() {
  ip=$(hostname -I | awk '{print $1}')
  mac=$(ip link | awk '/link\/ether/ {print $2}')
  echo "IP $ip (MAC: $mac)"
}

get_sudo_command_count() {
  journalctl _COMM=sudo | grep COMMAND | wc -l
}

# Display system information
wall "
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                  SYSTEM INFORMATION           
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
$(print_info "Architecture" "$(get_architecture)")
$(print_info "CPU Info" "$(get_cpu_info)")
$(print_info "Memory Usage" "$(get_memory_info)")
$(print_info "Disk Usage" "$(get_disk_info)")
$(print_info "CPU Load" "$(get_cpu_load)")
$(print_info "Last Boot" "$(get_last_boot)")
$(print_info "LVM Active" "$(get_lvm_status)")
$(print_info "TCP Connections" "$(get_tcp_connections) established")
$(print_info "Logged-in Users" "$(get_logged_in_users)")
$(print_info "Network" "$(get_network_info)")
$(print_info "Sudo Commands Used" "$(get_sudo_command_count)")
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
"

```

---

## **4. File Searching Commands**

- **Find a File by Name**:
    
    ```bash
    find /path/to/search -name "filename"
    
    ```
    
- **Search Case-Insensitive**:
    
    ```bash
    find /path/to/search -iname "filename"
    
    ```
    
- **Locate Command**:
    - Update database:
        
        ```bash
        sudo updatedb
        
        ```
        
    - Search:
        
        ```bash
        locate filename
        
        ```
        

---

## **5. Useful Commands**

- **Check Running Services**:
    
    ```bash
    sudo systemctl list-units --type=service
    
    ```
    
- **List All Open Ports**:
    
    ```bash
    sudo ss -tuln
    
    ```
    
- **Display Disk Usage**:
    
    ```bash
    df -h
    
    ```
    
- **Show RAM Usage**:
    
    ```bash
    free -h
    
    ```
    
- **Logged-in Users**:
    
    ```bash
    who
    
    ```
    

---

## **6. Bonus Section**

- **Partitions**:
    - Verify partition structure:
        
        ```bash
        lsblk
        
        ```
        
- **WordPress Setup**:
    - Ensure services are running:
        
        ```bash
        sudo systemctl status lighttpd mariadb
        
        ```
        
    - Check PHP integration with Lighttpd.
