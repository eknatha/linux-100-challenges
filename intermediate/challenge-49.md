# Challenge 49: Analyze Logs for Errors

## 🧩 Task
Search logs for errors.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Search for errors (case-insensitive)
```bash
grep -i "error" /var/log/messages
```

---

### 🔹 Search HTTP 503 errors in access logs
```bash
grep -E "\" 503 " access.log | tail -n 5
```

**Example output:**
```text
192.168.1.10 - - [19/Mar/2026:10:10:01] "GET /api HTTP/1.1" 503 512
192.168.1.11 - - [19/Mar/2026:10:10:05] "POST /login HTTP/1.1" 503 256
```

---

### 🔹 Search errors in syslog (Debian/Ubuntu)
```bash
grep "error" /var/log/syslog
```

---

### 📖 Explanation
- `grep` → Searches for patterns in files  
- `-i` → Case-insensitive search  
- `-E` → Extended regex (for advanced patterns)  
- `"error"` → Keyword being searched  
- `tail -n 5` → Shows last 5 matching lines  

---

### ⚠️ System Differences
- **RHEL / CentOS / Amazon Linux:**
```bash
grep -i "error" /var/log/messages
```

- **Ubuntu / Debian:**
```bash
grep -i "error" /var/log/syslog
```

---

### 💡 Tips
- Follow logs and filter in real-time:
```bash
tail -f /var/log/syslog | grep -i error
```

- Show line numbers:
```bash
grep -n "error" file.log
```

- Count occurrences:
```bash
grep -c "error" file.log
```

---

### 🚀 Use Cases
- Debugging application failures  
- Identifying HTTP errors (5xx issues)  
- Monitoring system logs for anomalies  

</details>
