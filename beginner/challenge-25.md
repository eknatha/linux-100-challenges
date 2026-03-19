# Challenge 25: Environment Variables

## 🧩 Task
Set an environment variable.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Set an environment variable
```bash
export MY_VAR="hello"
```

### 🔹 Access the variable
```bash
echo $MY_VAR
```

**Example output:**
```text
hello
```

### 📖 Explanation
- `export` → Sets an environment variable available to the current shell and child processes  
- `MY_VAR="hello"` → Assigns the value `hello` to the variable `MY_VAR`  
- `$MY_VAR` → Used to reference the value of the variable  

**Tip:**
- Environment variables set this way are temporary (lost after session ends)  
- To make them permanent, add them to shell config files like:
  - `~/.bashrc`
  - `~/.zshrc`

</details>
