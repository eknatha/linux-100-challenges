# Challenge 52: Use Loops in Bash

![Bash](https://img.shields.io/badge/Shell-Bash-4EAA25?style=flat&logo=gnu-bash&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner-brightgreen)

## 📌 Overview

Loops in Bash allow you to **repeat commands** multiple times without rewriting them. Bash supports three types of loops — `for`, `while`, and `until` — each suited for different use cases.

---

## 🧩 Task

Print numbers from 1 to 5 using a loop.

---

<details>
<summary>💡 Click to view solution</summary>

## ✅ Solution

```bash
#!/bin/bash
for i in {1..5}
do
  echo $i
done
```

```bash
$ ./script.sh
1
2
3
4
5
```

### How it works

| Part | Description |
|------|-------------|
| `for i in {1..5}` | Iterates `i` over the sequence 1, 2, 3, 4, 5 |
| `{1..5}` | Brace expansion — generates a range of numbers |
| `do` | Marks the beginning of the loop body |
| `echo $i` | Prints the current value of `i` |
| `done` | Marks the end of the loop |

---

## 📖 Extended Examples

### Example 1 — Basic Range Loop

```bash
#!/bin/bash
for i in {1..5}
do
  echo $i
done
```

```bash
1
2
3
4
5
```

---

### Example 2 — Loop with a Step / Increment

```bash
#!/bin/bash
# Print even numbers from 2 to 10
for i in {2..10..2}
do
  echo $i
done
```

```bash
2
4
6
8
10
```

> 💡 `{start..end..step}` — the third value sets the step size.

---

### Example 3 — C-Style `for` Loop

```bash
#!/bin/bash
for (( i=1; i<=5; i++ ))
do
  echo "Number: $i"
done
```

```bash
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
```

> 💡 C-style loops give fine-grained control over initialization, condition, and increment.

---

### Example 4 — Loop Over a List of Strings

```bash
#!/bin/bash
for fruit in Apple Banana Cherry Mango
do
  echo "Fruit: $fruit"
done
```

```bash
Fruit: Apple
Fruit: Banana
Fruit: Cherry
Fruit: Mango
```

---

### Example 5 — `while` Loop

```bash
#!/bin/bash
i=1
while [ $i -le 5 ]
do
  echo "Count: $i"
  ((i++))
done
```

```bash
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

> 💡 `while` runs as long as the condition is **true**.

---

### Example 6 — `until` Loop

```bash
#!/bin/bash
i=1
until [ $i -gt 5 ]
do
  echo "Until: $i"
  ((i++))
done
```

```bash
Until: 1
Until: 2
Until: 3
Until: 4
Until: 5
```

> 💡 `until` runs as long as the condition is **false** — the opposite of `while`.

---

### Example 7 — Loop Over Files in a Directory

```bash
#!/bin/bash
for file in *.txt
do
  echo "Found file: $file"
done
```

```bash
Found file: notes.txt
Found file: log.txt
Found file: readme.txt
```

---

### Example 8 — Loop with `break` and `continue`

```bash
#!/bin/bash
for i in {1..10}
do
  if [ $i -eq 4 ]; then
    echo "Skipping $i"
    continue       # Skip the rest of this iteration
  fi

  if [ $i -eq 7 ]; then
    echo "Stopping at $i"
    break          # Exit the loop entirely
  fi

  echo $i
done
```

```bash
1
2
3
Skipping 4
5
6
Stopping at 7
```

---

## 🔄 Loop Types — Quick Comparison

| Loop | Best Used When |
|------|----------------|
| `for i in {1..N}` | You know the range upfront |
| `for (( i=0; i<N; i++ ))` | You need C-style control (custom step, complex condition) |
| `for item in list` | Iterating over a fixed list of words or files |
| `while [ condition ]` | Repeating while a condition stays true |
| `until [ condition ]` | Repeating until a condition becomes true |

---

## 🚀 How to Run

```bash
# Step 1: Create the script
touch loop.sh

# Step 2: Make it executable
chmod +x loop.sh

# Step 3: Run it
./loop.sh
```

---

## 🔑 Key Takeaways

- `{1..5}` is **brace expansion** — a concise way to generate number sequences.
- `{start..end..step}` adds a **step** to control the increment.
- Use **C-style** `for (( ))` when you need maximum control over loop variables.
- `while` loops on a **true** condition; `until` loops on a **false** condition.
- `break` exits a loop early; `continue` skips to the next iteration.

---

## 📚 Related Concepts

- [Bash Brace Expansion](https://www.gnu.org/software/bash/manual/bash.html#Brace-Expansion)
- [Bash Looping Constructs](https://www.gnu.org/software/bash/manual/bash.html#Looping-Constructs)
- [Arithmetic in Bash](https://www.gnu.org/software/bash/manual/bash.html#Arithmetic-Expansion)

</details>
