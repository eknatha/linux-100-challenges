# Challenge 21: Find Files

## 🧩 Task
Search for files.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Find a file by name
```bash
find / -name "file.txt"
```

**Example output:**
```text
/home/user/file.txt
/var/log/file.txt
```

### 📖 Explanation
- `find` → Command used to search files and directories  
- `/` → Starting directory (root directory in this case)  
- `-name "file.txt"` → Searches for files matching the exact name  
- Output lists all matching file paths found in the system  

**Tip:**
- Use `find . -name "file.txt"` to search in the current directory  
- Use `-iname` for case-insensitive search:
```bash
find / -iname "file.txt"
```

</details>
