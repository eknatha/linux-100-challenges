# Challenge 23: Compress Files

## 🧩 Task
Compress a file.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Compress a file using tar
```bash
tar -czvf file.tar.gz file.txt
```

**Example output:**
```text
file.txt
```

### 📖 Explanation
- `tar` → Archive utility used to combine files  
- `-c` → Create a new archive  
- `-z` → Compress using gzip  
- `-v` → Verbose (shows files being processed)  
- `-f` → Specifies the output file name  

**Result:**
- `file.txt` is compressed into `file.tar.gz`

**Tip:**
To compress multiple files:
```bash
tar -czvf archive.tar.gz file1.txt file2.txt
```

</details>
