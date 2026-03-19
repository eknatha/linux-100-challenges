# Challenge 24: Extract Files

## 🧩 Task
Extract a compressed file.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Extract a tar.gz file
```bash
tar -xzvf file.tar.gz
```

**Example output:**
```text
file.txt
```

### 📖 Explanation
- `tar` → Archive utility  
- `-x` → Extract files from an archive  
- `-z` → Decompress gzip archive  
- `-v` → Verbose (shows extracted files)  
- `-f` → Specifies the archive file  

**Result:**
- `file.tar.gz` is extracted back into its original file(s)

**Tip:**
To extract into a specific directory:
```bash
tar -xzvf file.tar.gz -C /path/to/directory/
```

</details>
