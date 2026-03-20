# Challenge 42: Check Network Connectivity

## 🧩 Task
Test connectivity to a host.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Check connectivity using ping
```bash
ping google.com
```

**Example output:**
```text
PING google.com (142.250.183.206) 56(84) bytes of data.
64 bytes from 142.250.183.206: icmp_seq=1 ttl=115 time=20.5 ms
64 bytes from 142.250.183.206: icmp_seq=2 ttl=115 time=19.8 ms
```

---


---

### 📖 Explanation
- `ping` → Tests connectivity by sending ICMP packets to a host  
- Shows:
  - Response time (latency)  
  - Packet loss  

---

### 💡 Tips
- Stop `ping` using `Ctrl + C`  
- Send limited packets:
```bash
ping -c 4 google.com
```

---

### 🚀 Use Cases
- Debugging network connectivity issues  
- Identifying slow network paths  
- Verifying external service availability  

</details>
