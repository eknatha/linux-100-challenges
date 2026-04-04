# Challenge 86: Troubleshoot DNS Issues in Linux

![Bash](https://img.shields.io/badge/Shell-Bash-4EAA25?style=flat&logo=gnu-bash&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-yellow)


![Eknatha](https://img.shields.io/badge/Eknatha-4EAA25?style=flat&logo=gnu-bash&logoColor=white)

## 📌 Overview

**DNS (Domain Name System)** translates human-readable hostnames like `google.com` into IP addresses that computers use to communicate. DNS failures cause applications to be unreachable even when the network is physically fine — making DNS troubleshooting a critical sysadmin skill.

Common DNS problems include:
- Wrong or unreachable DNS server configured
- DNS server returning wrong results (misconfiguration or cache poisoning)
- Missing or incorrect DNS records on the authoritative server
- Slow DNS resolution adding latency to every connection
- `/etc/resolv.conf` misconfigured or overwritten
- Firewall blocking UDP/TCP port 53

---

## 🧩 Task

Diagnose and resolve DNS resolution failures using `nslookup` and `dig`.

---


<details>
<summary>💡 Click to view solution</summary>


## ✅ Solution

```bash
# Quick DNS lookup
nslookup google.com

# Detailed DNS query with full response
dig google.com
```

### How it works

| Command | Description |
|---------|-------------|
| `nslookup google.com` | Query the configured DNS server for `google.com` and display the result |
| `dig google.com` | **D**omain **I**nformation **G**roper — detailed DNS query with full response, flags, and timing |

---

## 🔢 Understanding `nslookup` Output

```bash
nslookup google.com
```

```
Server:         192.168.1.1
Address:        192.168.1.1#53

Non-authoritative answer:
Name:    google.com
Address: 142.250.80.46
```

| Field | Description |
|-------|-------------|
| `Server` | The DNS server used to resolve the query |
| `Address` | DNS server IP and port (53 = DNS) |
| `Non-authoritative answer` | Response from a cache — not the authoritative DNS server |
| `Name` | The hostname queried |
| `Address` | The resolved IP address |

---

## 🔢 Understanding `dig` Output

```bash
dig google.com
```

```
; <<>> DiG 9.18.1 <<>> google.com
;; global options: +cmd

;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             300     IN      A       142.250.80.46

;; Query time: 8 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Thu Mar 21 10:35:22 UTC 2025
;; MSG SIZE  rcvd: 55
```

| Section | Description |
|---------|-------------|
| `status: NOERROR` | Query succeeded — `SERVFAIL`, `NXDOMAIN`, `REFUSED` indicate problems |
| `flags: qr rd ra` | `qr`=response, `rd`=recursion desired, `ra`=recursion available |
| `QUESTION SECTION` | The query sent |
| `ANSWER SECTION` | The resolved records |
| `Query time` | **How long DNS took** — high values indicate slow resolver |
| `SERVER` | Which DNS server answered |

### DNS Status Codes

| Status | Meaning | Common Cause |
|--------|---------|-------------|
| `NOERROR` | Success — record found | — |
| `NXDOMAIN` | **Non-existent domain** — hostname does not exist | Typo in domain, DNS record deleted |
| `SERVFAIL` | **Server failure** — DNS server could not resolve | DNS server error, upstream failure |
| `REFUSED` | Server refused the query | Access control on DNS server |
| `FORMERR` | Format error in query | Malformed DNS request |

---

## 📖 Extended Examples

### Example 1 — Basic DNS Lookup

```bash
# nslookup
nslookup google.com

# dig (more detailed)
dig google.com

# host (simplest output)
host google.com
```

```bash
# host output:
google.com has address 142.250.80.46
google.com has IPv6 address 2607:f8b0:4004:c1b::64
google.com mail is handled by 10 smtp.google.com.
```

---

### Example 2 — Query a Specific DNS Server

Test against a specific DNS server to bypass your local resolver:

```bash
# Query Google's public DNS (8.8.8.8)
nslookup google.com 8.8.8.8
dig @8.8.8.8 google.com

# Query Cloudflare's DNS (1.1.1.1)
dig @1.1.1.1 google.com

# Query your local DNS server directly
dig @192.168.1.1 google.com

# Query against OpenDNS
dig @208.67.222.222 google.com
```

> 💡 If `dig @8.8.8.8 google.com` works but `dig google.com` fails, the problem is your **local DNS configuration**, not the network or the domain.

---

### Example 3 — Query Specific DNS Record Types

```bash
# A record (IPv4 address) — default
dig google.com A

# AAAA record (IPv6 address)
dig google.com AAAA

# MX record (mail server)
dig google.com MX

# NS record (nameservers)
dig google.com NS

# TXT record (SPF, DMARC, verification tokens)
dig google.com TXT

# CNAME record (alias)
dig www.github.com CNAME

# SOA record (start of authority)
dig google.com SOA

# Any available records
dig google.com ANY

# PTR record (reverse DNS — IP to hostname)
dig -x 142.250.80.46
```

---

### Example 4 — Clean `dig` Output (Just the Answer)

```bash
# +short: print only the answer, no headers
dig +short google.com

# +short for MX records
dig +short google.com MX

# +noall +answer: show only the answer section
dig +noall +answer google.com
```

```bash
# dig +short output:
142.250.80.46

# dig +short MX output:
10 smtp.google.com.

# dig +noall +answer output:
google.com.   300   IN   A   142.250.80.46
```

---

### Example 5 — Check DNS Propagation (Trace the Full Path)

```bash
# Trace the full resolution path from root DNS servers
dig +trace google.com
```

```bash
# Trace output:
.               518400  IN  NS  a.root-servers.net.
com.            172800  IN  NS  a.gtld-servers.net.
google.com.     172800  IN  NS  ns1.google.com.
google.com.     300     IN  A   142.250.80.46
```

> 💡 `+trace` starts from the root DNS servers and follows the delegation chain — it shows exactly where DNS resolution succeeds or fails.

---

### Example 6 — Check `/etc/resolv.conf` Configuration

```bash
# View current DNS configuration
cat /etc/resolv.conf
```

```bash
# /etc/resolv.conf:
nameserver 192.168.1.1       # Primary DNS server
nameserver 8.8.8.8           # Fallback DNS server
search example.com           # Default search domain
options timeout:2 attempts:3 # Retry settings
```

```bash
# On systemd-resolved systems:
resolvectl status
resolvectl dns

# Check which DNS server is actually being used
resolvectl query google.com
```

---

### Example 7 — Flush the DNS Cache

If DNS is returning stale or incorrect cached results:

```bash
# Flush systemd-resolved cache (most modern distros)
sudo systemd-resolve --flush-caches
# OR
sudo resolvectl flush-caches

# Verify cache was flushed
sudo resolvectl statistics

# Flush nscd cache (if nscd is running)
sudo nscd -i hosts

# Restart the resolver service
sudo systemctl restart systemd-resolved

# On RHEL — restart NetworkManager (manages resolv.conf)
sudo systemctl restart NetworkManager
```

---

### Example 8 — Test DNS Performance (Latency)

```bash
# Check query time from dig output
dig google.com | grep "Query time"

# Test multiple DNS servers for speed
for dns in 8.8.8.8 8.8.4.4 1.1.1.1 208.67.222.222; do
  echo -n "$dns: "
  dig @$dns google.com | grep "Query time"
done
```

```bash
8.8.8.8: ;; Query time: 12 msec
8.8.4.4: ;; Query time: 14 msec
1.1.1.1: ;; Query time: 8 msec
208.67.222.222: ;; Query time: 22 msec
```

---

### Example 9 — Check Reverse DNS (PTR Records)

```bash
# Reverse lookup — IP to hostname
nslookup 142.250.80.46
dig -x 142.250.80.46
host 142.250.80.46

# Check PTR record for your server's IP
dig -x $(curl -s ifconfig.me)
```

```bash
# Output:
46.80.250.142.in-addr.arpa. 300 IN PTR lga34s15-in-f14.1e100.net.
```

> 💡 Missing or incorrect PTR records cause **email delivery failures** — mail servers use reverse DNS to verify sender legitimacy.

---

### Example 10 — Diagnose DNS with `systemd-resolved`

```bash
# Check resolver status
resolvectl status

# Query using systemd-resolved
resolvectl query google.com

# Show DNS statistics
resolvectl statistics

# Show LLMNR and mDNS status
resolvectl status | grep -E "LLMNR|mDNS|DNSSec"

# Check the stub resolver
cat /etc/resolv.conf
# Should show: nameserver 127.0.0.53
```

---

### Example 11 — Check Firewall for DNS Blocking

```bash
# Test if UDP port 53 is reachable (DNS uses UDP by default)
nc -uvz 8.8.8.8 53

# Test TCP port 53 (used for large responses and AXFR)
nc -vz 8.8.8.8 53

# Check firewall rules for port 53
sudo firewall-cmd --list-all | grep dns   # firewalld
sudo ufw status | grep 53                  # ufw
sudo iptables -L -n | grep 53             # iptables

# Force dig to use TCP (bypasses UDP firewall rules)
dig +tcp google.com
```

---

### Example 12 — Full DNS Troubleshooting Script

```bash
#!/bin/bash

TARGET="${1:-google.com}"
DNS_SERVER="${2:-8.8.8.8}"

echo "============================================"
echo " DNS Troubleshooting Report"
echo " Target: $TARGET | Reference DNS: $DNS_SERVER"
echo " $(date)"
echo "============================================"

echo -e "\n[1] /etc/resolv.conf]"
cat /etc/resolv.conf

echo -e "\n[2] Local DNS Resolution]"
time dig +short "$TARGET" 2>&1

echo -e "\n[3] Resolution via $DNS_SERVER]"
dig @"$DNS_SERVER" +short "$TARGET" 2>&1

echo -e "\n[4] Query Time Comparison]"
for dns in 8.8.8.8 1.1.1.1 9.9.9.9; do
  echo -n "  $dns: "
  dig @$dns "$TARGET" 2>/dev/null | grep "Query time" | awk '{print $4, $5}'
done

echo -e "\n[5] DNS Trace (root → authoritative)]"
dig +trace +short "$TARGET" 2>&1 | tail -5

echo -e "\n[6] Record Types]"
for type in A AAAA MX NS TXT; do
  result=$(dig +short "$TARGET" $type 2>/dev/null)
  echo "  $type: ${result:-no record}"
done

echo -e "\n[7] DNS Cache Status (systemd-resolved)]"
resolvectl statistics 2>/dev/null | grep -E "Cache|Queries"

echo -e "\n[8] Reverse DNS for first resolved IP]"
IP=$(dig +short "$TARGET" | head -1)
dig -x "$IP" +short 2>/dev/null || echo "No PTR record"
```

---

## 🗂️ DNS Record Types — Quick Reference

| Record | Purpose | Example |
|--------|---------|---------|
| `A` | IPv4 address | `google.com → 142.250.80.46` |
| `AAAA` | IPv6 address | `google.com → 2607:f8b0::` |
| `CNAME` | Canonical name (alias) | `www → google.com` |
| `MX` | Mail exchange server | `google.com → smtp.google.com` |
| `NS` | Nameserver | `google.com → ns1.google.com` |
| `TXT` | Text (SPF, DKIM, verification) | `google.com → "v=spf1 ..."` |
| `PTR` | Reverse DNS (IP → hostname) | `46.80.250.142 → lga34s15.1e100.net` |
| `SOA` | Start of authority | Zone admin and refresh info |
| `SRV` | Service location | `_http._tcp → hostname:port` |
| `CAA` | Certificate authority auth | Which CAs can issue certs |

---

## 🛠️ DNS Troubleshooting Tools — Comparison

| Tool | Best For |
|------|----------|
| `nslookup` | Quick lookups, interactive mode, cross-platform |
| `dig` | Detailed queries, scripting, full response inspection |
| `host` | Simple human-readable output |
| `resolvectl` | systemd-resolved management and statistics |
| `dig +trace` | Tracing delegation from root to authoritative |
| `dig @server` | Testing a specific DNS server directly |
| `nc -uvz host 53` | Testing UDP/TCP port 53 reachability |

---

## 🚀 Full Workflow — Diagnose a DNS Failure

```bash
# Step 1: Confirm DNS is failing
ping google.com       # Fails with "unknown host"
curl https://google.com   # Fails with "Could not resolve host"

# Step 2: Test with a direct IP to confirm network is up
ping 8.8.8.8    # If this works, the network is fine — DNS is the problem

# Step 3: Check your configured DNS server
cat /etc/resolv.conf
resolvectl status

# Step 4: Try resolving with a known public DNS server
dig @8.8.8.8 google.com

# Step 5: If Step 4 works, your local resolver is the problem
# Fix: update /etc/resolv.conf or restart systemd-resolved
sudo resolvectl flush-caches
sudo systemctl restart systemd-resolved

# Step 6: If Step 4 fails too, check if port 53 is blocked
nc -uvz 8.8.8.8 53
sudo iptables -L -n | grep 53

# Step 7: Trace the resolution path
dig +trace google.com

# Step 8: Check all record types for the domain
dig google.com ANY

# Step 9: Test DNS speed and compare servers
for dns in 8.8.8.8 1.1.1.1 9.9.9.9; do
  echo -n "$dns: "; dig @$dns google.com | grep "Query time"
done

# Step 10: Update DNS config if needed
sudo nano /etc/resolv.conf
# Add: nameserver 8.8.8.8
```

---

## ⚠️ Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| `nslookup` succeeds but `ping hostname` fails | Check `/etc/nsswitch.conf` — `hosts` line order matters |
| `/etc/resolv.conf` changes are overwritten | Edit via NetworkManager or `systemd-resolved` — not directly |
| DNS works for A records but not others | Check `dig google.com AAAA` — firewall may block IPv6 DNS |
| Cached wrong answer still resolving | `sudo resolvectl flush-caches` then retest |
| `SERVFAIL` from local resolver | Try `dig @8.8.8.8` — upstream resolver may be down |
| Slow DNS on first query, fast afterwards | Normal — first query misses cache; subsequent queries are cached |
| `dig +trace` shows delegation but resolution fails | DNSSEC validation failure — check `dig +dnssec google.com` |

---

## 🔑 Key Takeaways

- `nslookup` gives a quick answer; `dig` gives the full picture including query time, status, and all sections.
- **`dig @8.8.8.8 google.com`** bypasses your local resolver — if this works but `dig google.com` fails, the problem is your local DNS config.
- The **DNS status code** (`NOERROR`, `NXDOMAIN`, `SERVFAIL`) immediately tells you what kind of failure you're dealing with.
- **`dig +trace`** follows the full delegation chain — use it to find exactly which server in the chain is failing.
- **`Query time`** in `dig` output measures DNS latency — compare multiple servers to find the fastest.
- `sudo resolvectl flush-caches` clears stale cached answers — always try this before investigating further.
- If `ping 8.8.8.8` succeeds but `ping google.com` fails — the network is fine, DNS is broken.

---

## 📚 Related Concepts

- [Challenge 75 — Debug Network Latency]
- [Challenge 66 — Configure Firewall]
- [Challenge 76 — Capture Network Traffic]
- [BIND DNS Server](https://www.isc.org/bind/) — the most widely deployed DNS server
- [systemd-resolved](https://www.freedesktop.org/software/systemd/man/systemd-resolved.service.html)
- [dig Manual](https://linux.die.net/man/1/dig)



</details>
