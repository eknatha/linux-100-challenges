# Challenge 82: Unauthorized Access Check in Linux

![Bash](https://img.shields.io/badge/Shell-Bash-4EAA25?style=flat&logo=gnu-bash&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-yellow)

![Eknatha](https://img.shields.io/badge/Eknatha-4EAA25?style=flat&logo=gnu-bash&logoColor=white)

## 📌 Overview

**Unauthorized access detection** is a core defensive security practice. Regularly auditing login history allows system administrators to identify:

- Logins from unexpected IP addresses or locations
- Logins at unusual times (e.g., 3 AM on a Sunday)
- Failed login attempts that suggest brute-force attacks
- Users who should not have remote access logging in
- Accounts that have been compromised

The combination of `last` (login history) and `/var/log/secure` (auth log) gives a complete picture of both successful and failed access attempts.

---

## 🧩 Task

Review login history and authentication logs to detect any unauthorized or suspicious access.

---


<details>
<summary>💡 Click to view solution</summary>


## ✅ Solution

```bash
# View recent login history
last

# View authentication log (RHEL/CentOS/Fedora)
cat /var/log/secure

# View authentication log (Debian/Ubuntu)
cat /var/log/auth.log
```

### How it works

| Command | Description |
|---------|-------------|
| `last` | Reads `/var/log/wtmp` — shows successful login/logout history |
| `cat /var/log/secure` | Raw auth log on **RHEL/CentOS** — all authentication events |
| `cat /var/log/auth.log` | Raw auth log on **Debian/Ubuntu** — all authentication events |

---

## 🔢 Understanding `last` Output

```bash
last
```

```
devuser  pts/0        192.168.1.5      Thu Mar 21 10:00   still logged in
adminuser pts/1       10.0.0.5         Thu Mar 21 09:45 - 10:30  (00:45)
root     tty1                          Thu Mar 20 22:10 - 22:15  (00:05)
badactor pts/2        203.0.113.99     Wed Mar 20 03:15 - 03:45  (00:30)
reboot   system boot  5.15.0-generic   Mon Mar 18 08:00

wtmp begins Mon Mar 18 08:00:00 2025
```

| Field | Description |
|-------|-------------|
| Username | Who logged in |
| Terminal | `pts/N` = SSH session; `tty` = local console |
| Source IP | Where they connected from |
| Date/Time | When the session started |
| Duration | How long the session lasted |
| `still logged in` | Session is currently active |
| `reboot` | System was rebooted at this time |

---

## 📖 Extended Examples

### Example 1 — View Recent Login History

```bash
last
```

Look for:
- Logins from **unexpected IP addresses**
- Logins at **unusual times** (middle of the night, weekends)
- **`root` logins** — root should rarely log in directly
- Logins from **foreign or unexpected countries**

---

### Example 2 — View Failed Login Attempts

```bash
# Failed logins only (requires root)
sudo lastb

# Count failed attempts per user
sudo lastb | awk '{print $1}' | sort | uniq -c | sort -rn | head -10

# Count failed attempts per IP
sudo lastb | awk '{print $3}' | sort | uniq -c | sort -rn | head -10
```

```bash
# lastb output:
root     ssh:notty    203.0.113.99    Wed Mar 20 03:10 - 03:10  (00:00)
root     ssh:notty    203.0.113.99    Wed Mar 20 03:10 - 03:10  (00:00)
admin    ssh:notty    203.0.113.99    Wed Mar 20 03:11 - 03:11  (00:00)

# Top attacking IPs:
 127  203.0.113.99
  45  198.51.100.5
```

> `lastb` reads `/var/log/btmp` — the failed login record.

---

### Example 3 — Check Last Login for Each User

```bash
# When did each user last log in?
lastlog

# Filter out users who have never logged in
lastlog | grep -v "Never logged in"

# Check last login for a specific user
lastlog -u devuser
```

```bash
Username         Port     From               Latest
root             tty1                        Thu Mar 20 22:10:05 2025
devuser          pts/0    192.168.1.5        Thu Mar 21 10:00:12 2025
adminuser        pts/1    10.0.0.5           Thu Mar 21 09:45:00 2025
mysql                                        **Never logged in**
nobody                                       **Never logged in**
```

> ⚠️ Service accounts (`mysql`, `www-data`, `nobody`) should **never** appear in `lastlog` with a real login — if they do, investigate immediately.

---

### Example 4 — Check Currently Logged-In Users

```bash
# Who is currently logged in?
who

# Detailed view of active sessions
w

# Simple list of usernames
users
```

```bash
# who output:
devuser  pts/0        2025-03-21 10:00 (192.168.1.5)
adminuser pts/1       2025-03-21 09:45 (10.0.0.5)

# w output (shows what each user is doing):
 10:35:22 up 3 days,  1:35,  2 users,  load average: 0.45, 0.42, 0.38
USER     TTY      FROM             LOGIN@   IDLE WHAT
devuser  pts/0    192.168.1.5      10:00    0.00s w
adminuser pts/1   10.0.0.5         09:45    5:10  bash
```

---

### Example 5 — Filter Auth Log for Failed Logins

```bash
# RHEL/CentOS
grep "Failed password" /var/log/secure | tail -20

# Debian/Ubuntu
grep "Failed password" /var/log/auth.log | tail -20
```

```bash
Mar 21 03:10:01 hostname sshd[4821]: Failed password for root from 203.0.113.99 port 44213 ssh2
Mar 21 03:10:03 hostname sshd[4822]: Failed password for root from 203.0.113.99 port 44214 ssh2
Mar 21 03:10:05 hostname sshd[4823]: Failed password for admin from 203.0.113.99 port 44215 ssh2
```

---

### Example 6 — Filter Auth Log for Successful Logins

```bash
# Show all accepted logins
grep "Accepted" /var/log/secure

# Show accepted password logins (should be rare if keys are enforced)
grep "Accepted password" /var/log/secure

# Show accepted key logins
grep "Accepted publickey" /var/log/secure
```

```bash
Mar 21 10:00:01 hostname sshd[1024]: Accepted publickey for devuser from 192.168.1.5 port 54312 ssh2
Mar 21 03:45:15 hostname sshd[4901]: Accepted password for root from 203.0.113.99 port 55001 ssh2
#                                                                   ^^^^^^^^^^^^^^^
#                                        ⚠️ Root login with password from external IP — suspicious!
```

---

### Example 7 — Identify Top Attacking IP Addresses

```bash
# Top IPs with failed SSH attempts
grep "Failed password" /var/log/secure | \
  awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -10
```

```bash
 127  203.0.113.99
  45  198.51.100.5
  23  203.0.113.88
   5  192.168.1.99
```

```bash
# Usernames being attacked
grep "Failed password" /var/log/secure | \
  awk '{print $9}' | sort | uniq -c | sort -rn | head -10
```

```bash
 127  root
  45  admin
  23  ubuntu
   5  postgres
```

---

### Example 8 — Check for Logins from Unexpected Countries (GeoIP)

```bash
# Install geoiplookup (optional)
sudo apt install geoip-bin    # Debian/Ubuntu
sudo dnf install GeoIP        # RHEL

# Check location of IPs in auth log
grep "Accepted" /var/log/secure | awk '{print $(NF-3)}' | sort -u | while read ip; do
  echo -n "$ip: "
  geoiplookup "$ip" 2>/dev/null | awk -F', ' '{print $2}' || echo "unknown"
done
```

```bash
192.168.1.5: Private IP
10.0.0.5:    Private IP
203.0.113.99: Country not expected here — investigate!
```

---

### Example 9 — Use `journalctl` for Auth Monitoring

```bash
# All SSH authentication events
journalctl -u sshd --since today

# Failed logins only
journalctl -u sshd --since today | grep "Failed"

# Successful logins only
journalctl -u sshd --since today | grep "Accepted"

# Sudo usage
journalctl _COMM=sudo --since today

# Real-time auth monitoring
journalctl -u sshd -f
```

---

### Example 10 — Detect Logins Outside Business Hours

```bash
# Find logins between 11 PM and 6 AM (potential unauthorized access)
last | awk '{
  if ($5 ~ /[0-9][0-9]:[0-9][0-9]/) {
    split($5, t, ":")
    if (t[1]+0 >= 23 || t[1]+0 <= 5) {
      print "⚠️  Off-hours login:", $0
    }
  }
}'
```

```bash
⚠️  Off-hours login: badactor pts/2  203.0.113.99  Wed Mar 20 03:15 - 03:45 (00:30)
```

---

### Example 11 — Monitor for New/Unexpected Users

```bash
# List all users with login shells (potential interactive accounts)
grep -E "/bin/bash|/bin/sh|/bin/zsh" /etc/passwd | awk -F: '{print $1, $3, $6}'

# List users added recently (check /etc/passwd modification)
ls -la /etc/passwd /etc/shadow

# Find recently created home directories
find /home -maxdepth 1 -type d -newer /etc/passwd -ls
```

---

### Example 12 — Full Unauthorized Access Audit Script

```bash
#!/bin/bash

LOG="${1:-/var/log/secure}"    # RHEL default; change to /var/log/auth.log for Debian
OUTPUT="/tmp/access_audit_$(date +%Y%m%d_%H%M%S).txt"
DATE=$(date +"%Y-%m-%d %H:%M:%S")

echo "============================================" | tee "$OUTPUT"
echo " Unauthorized Access Audit — $DATE"          | tee -a "$OUTPUT"
echo "============================================" | tee -a "$OUTPUT"

echo -e "\n[1] Recent Login History (last 20)]"    | tee -a "$OUTPUT"
last -20                                            | tee -a "$OUTPUT"

echo -e "\n[2] Currently Active Sessions]"         | tee -a "$OUTPUT"
w                                                   | tee -a "$OUTPUT"

echo -e "\n[3] Failed Login Attempts Today]"       | tee -a "$OUTPUT"
grep "Failed password" "$LOG" 2>/dev/null | grep "$(date '+%b %e')" | wc -l | \
  xargs -I{} echo "Total failed attempts today: {}" | tee -a "$OUTPUT"

echo -e "\n[4] Top Attacking IP Addresses]"        | tee -a "$OUTPUT"
grep "Failed password" "$LOG" 2>/dev/null | \
  awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -10 | tee -a "$OUTPUT"

echo -e "\n[5] Successful Logins Today]"           | tee -a "$OUTPUT"
grep "Accepted" "$LOG" 2>/dev/null | grep "$(date '+%b %e')" | tee -a "$OUTPUT"

echo -e "\n[6] Root Login Attempts]"               | tee -a "$OUTPUT"
grep "root" "$LOG" 2>/dev/null | grep -i "accept\|fail" | tail -10 | tee -a "$OUTPUT"

echo -e "\n[7] Sudo Usage Today]"                  | tee -a "$OUTPUT"
grep "sudo" "$LOG" 2>/dev/null | grep "$(date '+%b %e')" | tee -a "$OUTPUT"

echo -e "\n[8] Users Who Have Never Logged In]"    | tee -a "$OUTPUT"
lastlog | grep -v "Never logged in" | tail -n +2 | awk '{print $1, $4, $5, $6, $7, $8}' | tee -a "$OUTPUT"

echo -e "\nAudit report saved: $OUTPUT"
```

---

## 🗂️ Auth Log Patterns — Quick Reference

| Pattern | `grep` Filter | Meaning |
|---------|--------------|---------|
| Failed login | `"Failed password"` | Wrong password |
| Successful login | `"Accepted"` | Login succeeded |
| Invalid username | `"Invalid user"` | Username doesn't exist |
| Accepted with key | `"Accepted publickey"` | Key-based login |
| Accepted with password | `"Accepted password"` | Password login (should be rare) |
| Root login | `"for root"` | Root login attempt |
| Session opened | `"session opened"` | Login session started |
| Session closed | `"session closed"` | Login session ended |
| Sudo used | `"sudo:"` | Privilege escalation |
| Account locked | `"account locked"` | Too many failed attempts |

---

## 🔴 Red Flags to Always Investigate

| Red Flag | Why It Matters |
|----------|---------------|
| Root login via SSH | Root should use `sudo` — direct root SSH is a risk |
| Login with `Accepted password` when keys are enforced | Key-only policy may have been bypassed |
| Login from an unrecognized IP or country | Could indicate stolen credentials |
| Login at 3 AM on a Sunday | Attackers often operate outside business hours |
| Service account (`mysql`, `www-data`) appears in `last` | Service accounts should never have interactive logins |
| High volume of failed attempts followed by success | Brute force succeeded |
| User not in your team list appears in `last` | Unauthorized account or compromised credential |

---

## 🚀 Full Workflow — Investigate a Suspected Unauthorized Login

```bash
# Step 1: Check who has logged in recently
last | head -30

# Step 2: Check for failed attempts that preceded a success
grep "203.0.113.99" /var/log/secure | grep -E "Failed|Accepted"

# Step 3: Check what the session did (sudo, commands run)
grep "203.0.113.99\|devuser" /var/log/secure | grep -E "sudo|command"

# Step 4: Check if the session is still active
who
w

# Step 5: Terminate a suspicious active session
# Find the PID of the suspicious SSH session
ss -tnp | grep 203.0.113.99
# Kill the session
kill -HUP <sshd_PID>

# Step 6: Lock the compromised account immediately
sudo passwd -l devuser           # Lock password
sudo usermod -e 1 devuser        # Expire account

# Step 7: Rotate SSH keys for the account
sudo rm /home/devuser/.ssh/authorized_keys
# Then issue a new key from a trusted machine

# Step 8: Check for persistence mechanisms
crontab -l -u devuser
ls -la /home/devuser/.ssh/
ls -la /home/devuser/.bashrc /home/devuser/.bash_profile

# Step 9: Check if attacker escalated privileges
grep "devuser" /var/log/secure | grep "sudo"

# Step 10: Document and report
last > /tmp/login_evidence.txt
grep "devuser\|203.0.113.99" /var/log/secure > /tmp/auth_evidence.txt
```

---

## ⚠️ Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| `lastb` shows nothing | Needs root: `sudo lastb`; also check `/var/log/btmp` exists |
| `last` only shows recent history | Logs are rotated — check `/var/log/wtmp.*` for older history |
| Seeing IPs but can't geolocate | Install `geoiplookup` or check via `whois IP` |
| Failed to notice successful login after brute force | Always correlate `Failed password` with subsequent `Accepted` from same IP |
| Locked account still has active session | Locking prevents new logins — terminate existing session with `kill -HUP` |
| Auth log rolled over before investigation | Set longer retention in `logrotate` and `journald.conf` |

---

## 🔑 Key Takeaways

- `last` shows **successful login history** — always look at source IPs, times, and whether root logged in directly.
- `sudo lastb` shows **failed login attempts** — high counts from a single IP signal a brute-force attack.
- `lastlog` reveals **per-user last login times** — service accounts (`mysql`, `www-data`) should never appear.
- `w` and `who` show **currently active sessions** — use this to check if an attacker is still connected.
- Always correlate **failed attempts + subsequent success from the same IP** — that sequence indicates a successful brute force.
- Logins at odd hours and from unexpected IPs are the primary signals of unauthorized access.
- After confirming unauthorized access: **lock the account, terminate the session, rotate credentials, check for persistence** — in that order.

---

## 📚 Related Concepts

- [Challenge 65 — Restrict SSH Access]
- [Challenge 66 — Configure Firewall]
- [Challenge 67 — Monitor Authentication Logs]
- [Challenge 81 — Detect Suspicious Processes]
- [fail2ban](https://www.fail2ban.org/) — automatically ban IPs after repeated failed attempts
- [auditd](https://linux.die.net/man/8/auditd) — kernel-level audit framework for detailed access tracking

</details>
