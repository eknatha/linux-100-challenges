# Challenge 27: Check System Uptime

## 🧩 Task
Check system uptime.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Check system uptime
```bash
uptime
```

**Example output:**
```text
10:15:32 up 2 days,  3:21,  2 users,  load average: 0.15, 0.10, 0.05
```

### 📖 Explanation
- **10:15:32** → Current system time  
- **up 2 days, 3:21** → How long the system has been running  
- **2 users** → Number of logged-in users  
- **load average** → System load over the last:
  - 1 minute
  - 5 minutes
  - 15 minutes  

**Load average interpretation:**
- Lower values → less system load  
- Higher values → more CPU demand / potential overload  
- Compare with number of CPU cores for better context  

</details>
