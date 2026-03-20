# Challenge 50: Basic Bash Script

## 🧩 Task
Create a simple script.

---

<details>
<summary>💡 Click to view solution</summary>

### 🔹 Basic Bash script
```bash
#!/bin/bash
echo "Hello, World"
```

---

### 🔹 Save and run the script
```bash
chmod +x script.sh
./script.sh
```

**Example output:**
```text
Hello, World
```

---

### 🔹 Interactive script with user input and arrays
```bash
#!/bin/bash

# Greet user and request their name
echo "The activity generator"
read -p "What is your name? " name

# Create an array of activities
activity[0]="Football"
activity[1]="Table Tennis"
activity[2]="Cricket"
activity[3]="PS5"
activity[4]="Exercise"

array_length=${#activity[@]} # Store the length of the array
index=$(($RANDOM % $array_length)) # Randomly select an index

# Invite the user to participate
echo "Hi $name, would you like to play ${activity[$index]}?"
read -p "Answer: " answer
```

---

### 🔹 Example interaction (input/output)

**User runs script:**
```bash
./script.sh
```

**Output + Input flow:**
```text
The activity generator
What is your name? Alex
Hi Alex, would you like to play Cricket?
Answer: yes
```

---

### 📖 Explanation

**Basic script:**
- `#!/bin/bash` → Specifies Bash interpreter  
- `echo` → Prints output  

**Interactive script:**
- `read -p` → Prompts user and stores input  
- `activity[]` → Bash array storing multiple values  
- `${#activity[@]}` → Returns number of elements in array  
- `$RANDOM` → Generates a random number  
- `$(( ))` → Performs arithmetic operations  
- `${activity[$index]}` → Accesses array element  

---

### 💡 Tips
- Arrays start at index `0` in Bash  
- Always quote variables in echo for safety:
```bash
echo "Hi $name"
```
- `$RANDOM` is useful for simple random selection logic  
- Scripts should be made executable using `chmod +x`  

---

### 🚀 Use Cases
- Building interactive CLI tools  
- Automating DevOps workflows  
- Creating helper scripts for deployments or monitoring  

</details>
