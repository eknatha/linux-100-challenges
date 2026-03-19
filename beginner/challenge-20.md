# Challenge 20: Command History

## 🧩 Task
View command history.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 View command history
```bash
history
```

**Example output:**
```text
  1  ls -la
  2  cd /var/log
  3  cat syslog
  4  ps aux
  5  free -h
  6  history
```

### 📖 Explanation
- Displays a list of previously executed commands in the current shell session  
- Each command is assigned a number (history ID)  
- Useful for:
  - Re-running commands (`!n` where `n` is the command number)
  - Auditing user activity
  - Debugging workflows  

**Example (re-run a command):**
```bash
!3
```

</details>
