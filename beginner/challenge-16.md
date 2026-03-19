# Challenge 16: Check Disk Usage

## 🧩 Task
Check disk space usage.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Check overall disk space
```bash
df -h
```

**Example output:**
```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /
tmpfs           2.0G     0  2.0G   0% /dev/shm
```

---

### 🔹 Check directory-wise usage
```bash
du -sh *
```

**Example output:**
```text
4.0K  file1.txt
8.0K  file2.log
1.2M  mydir
```

</details>
