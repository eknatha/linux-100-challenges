# Challenge 48: Open Files by Process

## 🧩 Task
List files opened by processes.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 List all open files
```bash
lsof
```

---

### 🔹 Example output:
```text
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd      890 root  txt    REG  253,0   123456 1234 /usr/sbin/sshd
nginx    1203 user  mem    REG  253,0   654321 5678 /usr/sbin/nginx
python   2450 user  cwd    DIR  253,0     4096 9101 /home/user/app
```

---

### 📖 Explanation
- `lsof` → Lists open files and the processes using them  
- **COMMAND** → Process name  
- **PID** → Process ID  
- **USER** → Owner of the process  
- **FD** → File descriptor (e.g., cwd, txt, mem)  
- **TYPE** → File type (REG = file, DIR = directory)  
- **NAME** → File or resource being accessed  

---

### 💡 Tips
- Check files opened by a specific process:
```bash
lsof -p <PID>
```

- Check which process is using a specific file:
```bash
lsof /path/to/file
```

- Check open ports:
```bash
lsof -i :80
```

---

### 🚀 Use Cases
- Debugging file locks  
- Identifying processes using a file or port  
- Troubleshooting “file in use” errors  

</details>
