# Challenge 17: Check Memory Usage

## 🧩 Task
Check system memory.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Check memory usage
```bash
free -h
```

**Example output:**
```text
              total        used        free      shared  buff/cache   available
Mem:           8.0G        3.2G        1.5G        200M        3.3G        4.2G
Swap:          2.0G          0B        2.0G
```

### 📖 Explanation
- **total** → Total installed memory  
- **used** → Memory currently in use by processes  
- **free** → Completely unused memory  
- **shared** → Memory shared between processes  
- **buff/cache** → Memory used by kernel buffers and cache (can be reclaimed)  
- **available** → Estimated memory available for new applications without swapping  
- **Swap** → Disk-based memory used when RAM is full  

</details>
