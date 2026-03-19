# Challenge 72: Diagnose High Memory Usage

## 🧩 Task
Find processes consuming high memory.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Check overall memory usage
```bash
free -h
```

**Example output:**
```text
              total        used        free      shared  buff/cache   available
Mem:           8.0G        6.5G        500M        200M        1.0G        1.2G
Swap:          2.0G        500M        1.5G
```

---

### 🔹 Find top memory consuming processes
```bash
ps -eo pid,cmd,%mem --sort=-%mem | head
```

**Example output:**
```text
 PID CMD                         %MEM
1203 java -jar app.jar           25.5
2450 python app.py               15.2
 890 nginx: worker process        5.8
```

---

### 📖 Explanation
- `free -h` → Shows overall system memory usage in human-readable format  
- `%MEM` → Percentage of memory used by each process  
- `ps -eo ... --sort=-%mem` → Lists processes sorted by highest memory usage  
- `head` → Displays top memory-consuming processes  

**When to use:**
- Use `free -h` to check **system-level memory usage**  
- Use `ps` to identify **specific processes consuming memory**  

**Tip:**
- High memory usage with low **available memory** may indicate memory pressure  
- Check swap usage to detect potential memory exhaustion  

</details>
