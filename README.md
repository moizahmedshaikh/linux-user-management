# Assignment 1 — Linux User Management

**Course:** DevOps  
**Submitted by:** Moiz  
**Date:** April 2026

---

## Exercise 1 — Basic User Lifecycle

### Part A: Alice — Create, Configure, Delete

**Step 1: Create user alice with home directory**
```bash
sudo useradd -m alice
```

**Step 2: Confirm entry in /etc/passwd**
```bash
tail -3 /etc/passwd
```
Output:
```
polkitd:x:990:990:User for polkitd:/:/usr/sbin/nologin
moiz:x:1000:1000:,,,:/home/moiz:/bin/bash
alice:x:1001:1001::/home/alice:/bin/sh
```

**Step 3: Set password**
```bash
sudo passwd alice
# passwd: password updated successfully
```

**Step 4: Set full name (comment)**
```bash
sudo usermod -c "Alice" alice
```

**Step 5: Set default shell to /bin/bash**
```bash
sudo usermod -s /bin/bash alice
```

**Step 6: Switch to alice and verify**
```bash
su alice
whoami
# Output: alice
```

**Step 7: Return to original user**
```bash
exit
```

**Step 8: Delete alice with home directory**
```bash
sudo userdel -r alice
```

**Verification:**
```bash
tail -3 /etc/passwd
```
Output:
```
landscape:x:104:105::/var/lib/landscape:/usr/sbin/nologin
polkitd:x:990:990:User for polkitd:/:/usr/sbin/nologin
moiz:x:1000:1000:,,,:/home/moiz:/bin/bash
```
> Alice's entry is no longer present. ✅

---

### Part B: Bob — Advanced Create with All Flags

**Step 1: Create bob with single command (UID 1050, custom home, expiry)**
```bash
sudo useradd -u 1050 -d /opt/bob -c "Bob" -s /bin/bash -e 2027-06-01 -m bob
```

**Step 2: Verify entry**
```bash
cat /etc/passwd | grep bob
```
Output:
```
bob:x:1050:1050:Bob:/opt/bob:/bin/bash
```

**Step 3: Check password aging info**
```bash
sudo chage -l bob
```
Output:
```
Last password change                : Apr 06, 2026
Password expires                    : never
Password inactive                   : never
Account expires                     : Jun 01, 2027
Minimum number of days between password change : 0
Maximum number of days between password change : 99999
Number of days of warning before password expires : 7
```

**Step 4: Lock bob's account**
```bash
sudo usermod -L bob
```

**Step 5: Try su on locked account**
```bash
su bob
# Output: su: Authentication failure
```

> **Observation:** When `su` is attempted on a locked account, the system returns `Authentication failure`. This happens because `usermod -L` prepends a `!` to the password hash in `/etc/shadow`, making it invalid. Since no valid password exists, authentication is rejected immediately.

**Step 6: Unlock bob's account**
```bash
sudo usermod -U bob
# Note: Unlock warning appeared since bob had no password set — 
# this is expected behavior for a newly created user without a password
```

**Step 7: Delete bob with home directory**
```bash
sudo userdel -r bob
```

Verification:
```bash
cat /etc/passwd | grep bob
# (no output — bob removed) ✅
```

---

## Exercise 2 — Groups

**Step 1: Create developers group**
```bash
sudo groupadd developers
```

**Step 2: Create carol with primary group developers**
```bash
sudo useradd -m -g developers carol
```

**Step 3: Add carol to sudo group (supplementary)**
```bash
sudo usermod -aG sudo carol
```

**Step 4: Verify group entries**
```bash
cat /etc/group | grep -E "developers|sudo"
```
Output:
```
sudo:x:27:moiz,carol
developers:x:1001:
```

**Step 5: Set password on developers group**
```bash
sudo gpasswd developers
# Password set successfully
```

**Step 6: Verify group password in /etc/gshadow**
```bash
sudo cat /etc/gshadow | grep developers
```
Output:
```
developers:$6$5kX/UIhsX/VgUck9$5B5L5wgYocEKwVpk0xjVXKyG735n0OJdOCA71qp7ahSvP7BEbr5FBQwfLsu2aY8o..kGm3D8hbDFwH2fobKkb0::
```
> Hashed password stored in /etc/gshadow ✅

**Step 7: Cleanup**
```bash
sudo userdel -r carol
sudo groupdel developers
```

---

## Exercise 3 — Root Access, su, and sudo

**Step 1: Switch to root and check UID**
```bash
sudo su root
id
# Output: uid=0(root) gid=0(root) groups=0(root)
exit
```
> Root always has UID = 0

**Step 2: Try cd /root as normal user**
```bash
cd /root
# Output: -bash: cd: /root: Permission denied
```
> Normal users cannot access root's home directory

**Step 3: Create dave and add to sudo group**
```bash
sudo useradd -m -s /bin/bash dave
sudo passwd dave
sudo usermod -aG sudo dave
```

**Step 4: Switch to dave and try sudo cd /root**
```bash
su dave
sudo cd /root
# Output: sudo: cd: command not found
# sudo: "cd" is a shell built-in command, it cannot be run directly.
```

> `sudo cd` fails because `cd` is a **shell built-in** — it is not an external binary. `sudo` works by spawning a new child process with root privileges. Shell built-ins like `cd` only exist inside the shell process itself and cannot be launched as a separate process. Therefore, `sudo` has no binary to execute.

**Step 5: Try sudo ls /root as dave**
```bash
sudo ls /root
# Command succeeds (empty output = no files, but no error)
```

> `sudo ls` works because `ls` is a real binary located at `/bin/ls`. sudo can spawn it as a root-privileged child process. This is the key difference between built-ins and external commands.

**Step 6: Exit back to original user**
```bash
exit
```

---

## Key Commands Summary

| Command | Purpose |
|--------|---------|
| `useradd -m` | Create user with home directory |
| `usermod -c` | Set comment/full name |
| `usermod -s` | Change login shell |
| `usermod -L / -U` | Lock / Unlock account |
| `userdel -r` | Delete user with home directory |
| `groupadd` | Create a new group |
| `usermod -aG` | Append user to supplementary group |
| `gpasswd` | Set group password |
| `chage -l` | View password aging info |
| `sudo su root` | Switch to root |
| `id` | Show current user UID/GID |

---

## Key Observations

1. **Locked account login**: `usermod -L` prepends `!` to password hash — `su` returns `Authentication failure`
2. **sudo cd fails**: `cd` is a shell built-in, not a binary — sudo cannot execute it as a child process
3. **sudo ls works**: `ls` is an external binary at `/bin/ls` — sudo can run it with elevated privileges
4. **`-aG` vs `-G`**: Always use `-aG` when adding supplementary groups — `-G` alone replaces all existing groups
