# Challenge 43: Trace Network Route

## 🧩 Task
Trace route to a host.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Trace route to a host
```bash
traceroute google.com
```

---

### 🔹 Example output:
```text
1  192.168.1.1     1.123 ms
2  10.0.0.1        5.456 ms
3  172.16.0.1     10.789 ms
4  142.250.183.206 20.123 ms
```

---

### 📖 Explanation
- `traceroute` → Shows the path packets take to reach a destination  
- Each line represents a **network hop (router)**  
- Displays:
  - IP/hostname of each hop  
  - Time taken (latency)  

---

### 💡 Tips
- Identify where delay occurs by checking **high latency hops**  
- `* * *` in output → Packet loss or blocked ICMP at that hop  

- If not installed:
```bash
sudo apt install traceroute   # Debian/Ubuntu
sudo yum install traceroute   # RHEL/CentOS
```

---

### 🚀 Use Cases
- Troubleshooting network latency issues  
- Identifying routing problems  
- Debugging connectivity to external services  

</details>
