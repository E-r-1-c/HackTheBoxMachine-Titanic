# Titanic Machine Documentation

## Enumeration

### Initial Nmap Scan
**Command:**
nmap -sC -sV titanic.htb

**Results:**
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 73:03:9c:76:eb:04:f1:fe:c9:e9:80:44:9c:7f:13:46 (ECDSA)
|   256 d5:bd:1d:5e:9a:86:1c:eb:88:63:4d:5f:88:4b:7e:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Titanic - Book Your Ship Trip
|_http-server-header: Apache/2.4.52 (Ubuntu) | Werkzeug/3.0.3 Python/3.10.12
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

**Insights:**
- Investigated potential vulnerabilities related to **Werkzeug/3.0.3**, **Apache/2.4.52**, and **OpenSSH 8.9p1**, but found no applicable exploits.

---

### Gobuster Directory Scan
**Command:**
gobuster dir -u http://titanic.htb/ -w /usr/share/seclists/Discovery/Web-Content/common.txt

**Results:**
- `/book` (405 - Method Not Allowed)
- `/download` (400 - Bad Request)

**Steps Taken:**
- Explored `/book` and `/download` using curl and manual inspection, but no significant findings.
- Checked source code and browser developer tools for hints but found nothing useful.

---

### Subdomain Discovery
**Command Used (Tip from Online Resource):**
- Discovered subdomain: `dev.titanic.htb`.

**Configuration Update:**
Added the following entry to `/etc/hosts`:
```plaintext
10.129.173.50 dev.titanic.htb
```

---

## Subdomain Enumeration

### Follow-Up Nmap Scan
**Command:**
```bash
nmap -p- -T4 dev.titanic.htb
```

**Results:**
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52
```

### Gobuster Directory Scan
**Command:**
gobuster dir -u http://dev.titanic.htb/ -w /usr/share/seclists/Discovery/Web-Content/common.txt

**Results:**
- `/.well-known/change-password` (303 - Redirect to `/user/settings/account`)
- `/.well-known/openid-configuration` (200)
- `/.well-known/security.txt` (200)
- `/admin` (303 - Redirect to `/user/login`)
- `/administrator` (200)
- `/developer` (200)
- `/explore` (303 - Redirect to `/explore/repos`)
- `/favicon.ico` (301 - Redirect to `/assets/img/favicon.png`)
- `/issues` (303 - Redirect to `/user/login`)
- `/notifications` (303 - Redirect to `/user/login`)
- `/sitemap.xml` (200)
- `/v2` (401 - Unauthorized)

---

## Key Findings

### **Developer Path**
- `/developer` revealed a **docker-compose.yml** file:
```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    ports:
      - "127.0.0.1:3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: 'MySQLP@$$w0rd!'
      MYSQL_DATABASE: tickets
      MYSQL_USER: sql_svc
      MYSQL_PASSWORD: sql_password
    restart: always
```
- Credentials:
  - **MYSQL_ROOT_PASSWORD:** `MySQLP@$$w0rd!`
  - **MYSQL_USER:** `sql_svc`
  - **MYSQL_PASSWORD:** `sql_password`

---

### Reflection
- Dedicated time exploring directories, source code, and network activity before discovering the `dev` subdomain.
- Followed through on leads systematically, resulting in discovery of sensitive configuration files and credentials.

---

### Next Steps
1. Investigate MySQL using credentials found.
2. Explore interactive paths like `/admin`, `/administrator`, or `/v2` for further exploitation.
3. Test for Docker-related vulnerabilities or misconfigurations.

---
