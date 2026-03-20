# Challenge 74: Disk Latency Analysis

## 🧩 Task
Analyze disk performance.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Monitor disk performance in real-time
```bash
iostat -x 1
```

---

### 🔹 Example output:
```text
Device            r/s     w/s   rkB/s   wkB/s  await  svctm  %util
sda              15.2     8.5   200.0   150.0   12.5   3.2    85.0
sdb               2.0     1.0    20.0    10.0    5.0    1.5    10.0
```

---

### 📖 Explanation
- `iostat` → Reports CPU and disk I/O statistics  
- `-x` → Extended statistics (includes latency metrics)  
- `1` → Refresh interval in seconds  

**Key metrics:**
- **r/s, w/s** → Read/write operations per second  
- **rkB/s, wkB/s** → Data read/written per second  
- **await** → Average time (ms) for I/O requests (includes queue + service time)  
- **svctm** → Average service time per I/O request  
- **%util** → Percentage of time the disk is busy  

---

### ⚠️ How to interpret disk latency
- High **await** → Indicates latency issues  
- High **%util (~100%)** → Disk is saturated  
- Large gap between **await** and **svctm** → Queue congestion  

---

### 💡 Tips
- Install if not available:
```bash
sudo apt install sysstat   # Debian/Ubuntu
sudo yum install sysstat   # RHEL/CentOS
```

- Combine with process-level analysis:
```bash
iotop
```

---

### 🚀 Use Cases
- Diagnosing slow application performance  
- Identifying disk bottlenecks  
- Monitoring storage performance in production systems  

</details>
