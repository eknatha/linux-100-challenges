# Challenge 32: Top Memory Consuming Processes

## 🧩 Task
Find processes consuming the most memory.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Find top memory consuming processes
```bash
ps -eo pid,cmd,%mem --sort=-%mem | head
```

### 🔹 Example output:
```text
 PID CMD                         %MEM
1203 java -jar app.jar           25.5
2450 python app.py               15.2
 890 nginx: worker process        5.8
 450 /usr/sbin/sshd               1.2
```

### 📖 Explanation
- `ps` → Displays process information  
- `-e` → Shows all processes  
- `-o pid,cmd,%mem` → Custom output format:
  - **PID** → Process ID  
  - **CMD** → Command that started the process  
  - **%MEM** → Memory usage percentage  
- `--sort=-%mem` → Sorts processes by memory usage (highest first)  
- `head` → Displays top results  

**Tip:**
- Useful for identifying **memory-heavy applications**  
- Common in DevOps/SRE for troubleshooting memory leaks and performance issues  

</details>
