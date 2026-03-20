# Challenge 45: Fetch Data with curl

## 🧩 Task
Fetch a webpage.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Fetch webpage content
```bash
curl http://example.com
```

---

### 🔹 Example output:
```html
<!doctype html>
<html>
<head>
    <title>Example Domain</title>
</head>
<body>
    <h1>Example Domain</h1>
</body>
</html>
```

---

### 📖 Explanation
- `curl` → Command-line tool to transfer data from or to a server  
- `http://example.com` → URL of the webpage being requested  
- Output shows the **HTML content** of the webpage  

---

### 💡 Tips
- Fetch only headers:
```bash
curl -I http://example.com
```

- Save output to a file:
```bash
curl -o page.html http://example.com
```

- Follow redirects:
```bash
curl -L http://example.com
```

---

### 🚀 Use Cases
- Testing APIs and web services  
- Debugging HTTP responses  
- Automating data retrieval in scripts  

</details>
