# 🐧 Linux Shell Assignment

A complete shell scripting assignment covering variables, exit codes, conditionals, file checks, wildcards, and brace expansion in Bash (WSL2 Ubuntu).

---

## 📋 Table of Contents

1. [Variables & Display](#1-variables--display)
2. [User Input & Welcome Message](#2-user-input--welcome-message)
3. [Comments in Scripts](#3-comments-in-scripts)
4. [Exit Status with $?](#4-exit-status-with-)
5. [Exit Status Codes: 0, 127, 130](#5-exit-status-codes-0-127-130)
6. [&& Operator - Create & Enter Folder](#6--operator---create--enter-folder)
7. [|| Operator - Folder Exists Message](#7--operator---folder-exists-message)
8. [Both && and || in One Line](#8-both--and--in-one-line)
9. [Compare Numbers with -eq](#9-compare-numbers-with--eq)
10. [Find Greater Number with -gt](#10-find-greater-number-with--gt)
11. [-lt, -ge, -le Examples](#11--lt--ge--le-examples)
12. [Check if Directory Exists](#12-check-if-directory-exists)
13. [Check if Regular File](#13-check-if-regular-file)
14. [Readable, Writable, Executable Check](#14-readable-writable-executable-check)
15. [Compare Two Strings](#15-compare-two-strings)
16. [Empty Variable Check with -z](#16-empty-variable-check-with--z)
17. [Non-Empty Variable Check with -n](#17-non-empty-variable-check-with--n)
18. [Brace Expansion](#18-brace-expansion)
19. [Wildcards](#19-wildcards)
20. [Complete Script - User Input + Folder](#20-complete-script---user-input--folder)

---

## 1. Variables & Display

**File:** `q1_variables.sh`

```bash
#!/bin/bash

name="Moiz"
age=22
city="Karachi"

echo "Name: $name"
echo "Age: $age"
echo "City: $city"
```

**Output:**
```
Name: Moiz
Age: 22
City: Karachi
```

---

## 2. User Input & Welcome Message

**File:** `q2_input.sh`

```bash
#!/bin/bash

echo "Apna naam likhein:"
read user_name

echo "Welcome, $user_name!"
```

**Output:**
```
Apna naam likhein:
Moiz
Welcome, Moiz!
```

---

## 3. Comments in Scripts

**File:** `q3_comments.sh`

```bash
#!/bin/bash

# Ye script variables aur comments ka example hai

# User ka naam variable mein store karo
name="Moiz"

# Age store karo
age=22

# Screen pe print karo
echo "Name: $name"   # naam print hoga
echo "Age: $age"     # age print hoga
```

**Output:**
```
Name: Moiz
Age: 22
```

---

## 4. Exit Status with `$?`

**Commands run directly in terminal:**

```bash
ls
echo "Valid command exit status: $?"

blahblah
echo "Invalid command exit status: $?"
```

**Output:**
```
q1_variables.sh  q2_input.sh  q3_comments.sh
Valid command exit status: 0
bash: blahblah: command not found
Invalid command exit status: 127
```

> `$?` stores the exit status of the last executed command.

---

## 5. Exit Status Codes: 0, 127, 130

| Code | Meaning | Example |
|------|---------|---------|
| **0** | Success — command ran without error | `ls /home` |
| **127** | Command not found | `blahblah` (doesn't exist) |
| **130** | Script terminated with Ctrl+C | User pressed Ctrl+C during execution |

```bash
ls /home
echo $?        # Output: 0

fakecommand
echo $?        # Output: 127

# Run any script and press Ctrl+C
# echo $? => 130
```

---

## 6. `&&` Operator - Create & Enter Folder

```bash
mkdir myfolder && cd myfolder
```

**Output:**
```
(moves into myfolder — no output on success)
```

> `&&` = "Pehla command succeed kare tabhi doosra chalao"  
> If `mkdir` fails (folder exists), `cd` will NOT run.

---

## 7. `||` Operator - Folder Exists Message

```bash
mkdir myfolder || echo "Folder pehle se exist karta hai!"
```

**Output (if folder exists):**
```
mkdir: cannot create directory 'myfolder': File exists
Folder pehle se exist karta hai!
```

> `||` = "Pehla command fail kare tabhi doosra chalao"

---

## 8. Both `&&` and `||` in One Line

```bash
mkdir testfolder && echo "Folder ban gaya!" || echo "Folder banana fail hua!"
```

**Output (success):**
```
Folder ban gaya!
```

**Output (if already exists):**
```
mkdir: cannot create directory 'testfolder': File exists
Folder banana fail hua!
```

---

## 9. Compare Numbers with `-eq`

**File:** `q9_eq.sh`

```bash
#!/bin/bash

a=10
b=10

if [ $a -eq $b ]; then
    echo "Dono numbers equal hain"
else
    echo "Numbers equal nahi hain"
fi
```

**Output:**
```
Dono numbers equal hain
```

---

## 10. Find Greater Number with `-gt`

**File:** `q10_gt.sh`

```bash
#!/bin/bash

a=15
b=10

if [ $a -gt $b ]; then
    echo "$a bada hai"
else
    echo "$b bada hai"
fi
```

**Output:**
```
15 bada hai
```

---

## 11. `-lt`, `-ge`, `-le` Examples

**File:** `q11_comparisons.sh`

```bash
#!/bin/bash

a=5
b=10

if [ $a -lt $b ]; then
    echo "$a less than $b hai"
fi

if [ $b -ge $a ]; then
    echo "$b greater than or equal to $a hai"
fi

if [ $a -le $b ]; then
    echo "$a less than or equal to $b hai"
fi
```

**Output:**
```
5 less than 10 hai
10 greater than or equal to 5 hai
5 less than or equal to 10 hai
```

| Operator | Meaning |
|----------|---------|
| `-lt` | Less than |
| `-gt` | Greater than |
| `-le` | Less than or equal |
| `-ge` | Greater than or equal |
| `-eq` | Equal |
| `-ne` | Not equal |

---

## 12. Check if Directory Exists

```bash
if [ -d projects ]; then
    echo "projects directory exist karti hai"
else
    echo "projects directory exist nahi karti"
fi
```

**Output:**
```
projects directory exist nahi karti
```

> `-d` flag checks if a directory exists.

---

## 13. Check if Regular File

```bash
if [ -f script.sh ]; then
    echo "script.sh ek regular file hai"
else
    echo "script.sh file nahi hai"
fi
```

**Output:**
```
script.sh ek regular file hai
```

> `-f` flag checks if a regular file exists.

---

## 14. Readable, Writable, Executable Check

```bash
file="script.sh"

if [ -r $file ]; then echo "$file readable hai"; fi
if [ -w $file ]; then echo "$file writable hai"; fi
if [ -x $file ]; then echo "$file executable hai"; fi
```

**Output:**
```
script.sh readable hai
script.sh writable hai
script.sh executable hai
```

| Flag | Checks |
|------|--------|
| `-r` | Readable |
| `-w` | Writable |
| `-x` | Executable |

---

## 15. Compare Two Strings

**File:** `q15_strings.sh`

```bash
#!/bin/bash

str1="hello"
str2="hello"

if [ "$str1" = "$str2" ]; then
    echo "Strings match karti hain"
else
    echo "Strings match nahi karti"
fi
```

**Output:**
```
Strings match karti hain
```

> Strings ke liye `=` use hota hai, numbers ke liye `-eq`.

---

## 16. Empty Variable Check with `-z`

**File:** `q16_empty.sh`

```bash
#!/bin/bash

myvar=""

if [ -z "$myvar" ]; then
    echo "Variable empty hai"
else
    echo "Variable mein kuch hai"
fi
```

**Output:**
```
Variable empty hai
```

> `-z` = zero length string (empty)

---

## 17. Non-Empty Variable Check with `-n`

**File:** `q17_notempty.sh`

```bash
#!/bin/bash

myvar="Moiz"

if [ -n "$myvar" ]; then
    echo "Variable empty nahi hai: $myvar"
else
    echo "Variable empty hai"
fi
```

**Output:**
```
Variable empty nahi hai: Moiz
```

> `-n` = not empty

---

## 18. Brace Expansion

```bash
touch file{1,2,3,4}
ls file*
```

**Output:**
```
file1  file2  file3  file4
```

> Brace expansion se bash automatically `file1`, `file2`, `file3`, `file4` expand karta hai.

---

## 19. Wildcards

```bash
# H se start hone wali saari files
ls H*

# file ke baad exactly ek character wali files
ls file?
```

**Output:**
```
(H* — H se shuru hone wali koi file nahi)

file1  file2  file3  file4
```

| Wildcard | Meaning |
|----------|---------|
| `*` | Koi bhi characters (zero ya zyada) |
| `?` | Exactly ek character |

---

## 20. Complete Script - User Input + Folder

**File:** `q20_complete.sh`

```bash
#!/bin/bash

echo "Folder ka naam likhein:"
read folder_name

if [ -d "$folder_name" ]; then
    echo "ERROR: '$folder_name' pehle se exist karta hai!"
    exit 1
fi

mkdir "$folder_name"

if [ $? -eq 0 ]; then
    echo "SUCCESS: '$folder_name' ban gaya!"
else
    echo "ERROR: Folder banana fail hua!"
fi

echo "Final exit status: $?"
```

**Output (new folder):**
```
Folder ka naam likhein:
myproject
SUCCESS: 'myproject' ban gaya!
Final exit status: 0
```

**Output (existing folder):**
```
Folder ka naam likhein:
myproject
ERROR: 'myproject' pehle se exist karta hai!
```

---

## Quick Reference

```
$?              → Last command ka exit status
&&              → Pehla succeed ho tabhi doosra chale
||              → Pehla fail ho tabhi doosra chale
-eq -gt -lt     → Number comparison
-ge -le -ne     → Number comparison (continued)
-f -d           → File / Directory exist check
-r -w -x        → Read / Write / Execute permission
-z -n           → String empty / not-empty
*  ?            → Wildcards
{1,2,3}         → Brace expansion
```

---

## Environment

- **OS:** Ubuntu 24.04 (WSL2)
- **Shell:** GNU Bash 5.2
- **Machine:** Windows 11 + WSL2

---

*Assignment submitted by Moiz Ahmed Sheikh*