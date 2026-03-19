# Challenge 35: Manage System Services

## 🧩 Task
Start and enable a service.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Start a service
```bash
sudo systemctl start nginx
```

### 🔹 Enable service at boot
```bash
sudo systemctl enable nginx
```

---

### 🔹 Verify service status
```bash
sudo systemctl status nginx
```

**Example output:**
```text
● nginx.service - A high performance web server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
   Active: active (running) since Fri 2026-03-19 10:30:00
```

---

### 📖 Explanation
- `systemctl` → Command used to manage systemd services  
- `start` → Starts the service immediately  
- `enable` → Configures the service to start automatically at boot  
- `status` → Shows current state and logs of the service  

---

### 💡 Tips
- Restart a service:
```bash
sudo systemctl restart nginx
```

- Stop a service:
```bash
sudo systemctl stop nginx
```

- Disable service at boot:
```bash
sudo systemctl disable nginx
```

</details>
