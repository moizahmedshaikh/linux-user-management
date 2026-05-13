# DevOps Assignment – Linux Processes & Resource Monitoring

**Name:** Moiz Ahmed
**Course / Section:** DevOps Engineering 
**Date:** 13-5-2026

## Objective
Perform practical Linux terminal tasks related to process management, resource monitoring, CPU and memory analysis, signals, and basic system tuning.

## Requirements
- Linux system or virtual machine
- Terminal access
- Optional tools: `top`, `htop`, `ps`, `pgrep`, `pstree`, `free`, `vmstat`, `sar`, `nice`, `renice`, `kill`, `pkill`, `time`, `watch`

## Important Note
Some tasks create test processes only for learning purposes. You can safely remove them after the task using `kill` or `pkill`.

---

## 1) Find all running processes of the current user and identify which process is consuming the highest CPU

**Commands:**
```bash
whoami
ps -u "$USER" -o pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head -n 10
```

**Observation:**
- This lists all processes owned by the current user.
- The first process in the sorted list has the highest CPU usage.

---

## 2) Display system processes in real time and observe CPU and memory usage for at least 2 minutes

**Command:**
```bash
top
```

Or for a cleaner view:
```bash
htop
```

**Observation:**
- Watch CPU, memory, load average, and process states.
- Keep the terminal open for at least 2 minutes and note changes.

---

## 3) Identify a process running in the background and bring it to the foreground

**Create a background job:**
```bash
sleep 300 &
jobs -l
```

**Bring it to foreground:**
```bash
fg %1
```

**Observation:**
- `jobs -l` shows the job number and PID.
- `fg` moves the selected background job into the foreground.

---

## 4) Create a process that continuously consumes CPU resources and monitor its impact on system performance

**Create a CPU-consuming process:**
```bash
yes > /dev/null &
```

**Get its PID:**
```bash
pgrep -n yes
```

**Monitor it:**
```bash
top
```

**Stop it:**
```bash
kill <PID>
```

**Observation:**
- CPU usage rises sharply for the `yes` process.
- System load may increase depending on your CPU cores.

---

## 5) Identify a process with unusually high memory usage and record its PID and memory percentage

**Check memory-heavy processes:**
```bash
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -n 10
```

**Observation:**
- Note the PID, command name, and `%MEM` of the top process.
- On a real system, this is often a browser, editor, VM, or database.

---

## 6) Stop a running process gracefully and verify whether it terminated successfully

**Start a test process:**
```bash
sleep 300 &
PID=$!
```

**Send graceful termination signal:**
```bash
kill -15 "$PID"
```

**Verify termination:**
```bash
ps -p "$PID"
```

**Observation:**
- `SIGTERM` asks the process to exit cleanly.
- If `ps` shows no output, the process terminated successfully.

---

## 7) Force terminate a process that does not stop normally and confirm its removal from the process list

**Start a test process:**
```bash
sleep 300 &
PID=$!
```

**Force kill it:**
```bash
kill -9 "$PID"
```

**Confirm removal:**
```bash
ps -p "$PID"
```

**Observation:**
- `SIGKILL` stops the process immediately.
- Use it only when normal termination fails.

---

## 8) Run multiple background tasks simultaneously and monitor their states

**Start multiple jobs:**
```bash
sleep 200 &
sleep 250 &
sleep 300 &
jobs -l
```

**Monitor states:**
```bash
ps -o pid,stat,cmd -p $(jobs -p)
```

**Observation:**
- You should see multiple background jobs running.
- States like `S` usually mean sleeping.

---

## 9) Compare CPU load averages with actual CPU usage and explain the difference using system observations

**Commands:**
```bash
uptime
mpstat 1 5   # if sysstat is installed
```

If `mpstat` is unavailable:
```bash
top
```

**Observation:**
- `load average` shows the average number of runnable or waiting tasks.
- CPU usage shows how busy the processor is.
- Load can be high even when CPU is not fully busy if tasks are waiting on I/O.

---

## 10) Identify zombie or sleeping processes on the system and document their process states

**Find process states:**
```bash
ps -eo pid,ppid,stat,cmd | head -n 20
```

**Show sleeping processes:**
```bash
ps -eo pid,stat,cmd | awk '$2 ~ /^S/ {print}' | head
```

**Look for zombie processes:**
```bash
ps -eo pid,ppid,stat,cmd | awk '$3 ~ /Z/ {print}'
```

**Observation:**
- `S` = sleeping
- `R` = running
- `Z` = zombie

---

## 11) Simulate high CPU usage on the system and identify which process is responsible

**Run CPU stress:**
```bash
yes > /dev/null &
```

**Identify responsible process:**
```bash
top
```

or
```bash
ps -eo pid,cmd,%cpu --sort=-%cpu | head
```

**Stop it:**
```bash
pkill yes
```

**Observation:**
- The `yes` process should appear at the top of CPU-consuming processes.

---

## 12) Monitor available and used memory before and after running a heavy process

**Before:**
```bash
free -h
```

**Start a test workload:**
```bash
python3 -c 'a=["x"*1024*1024 for _ in range(200)]; input("Press Enter to exit...")' &
```

**After:**
```bash
free -h
ps -eo pid,cmd,%mem --sort=-%mem | head
```

**Observation:**
- Compare used and available memory before and after.
- The test process increases memory usage.

---

## 13) Restart a terminated process safely and confirm that it is functioning correctly again

**Start and stop a test process:**
```bash
sleep 300 &
PID=$!
kill -15 "$PID"
```

**Restart it:**
```bash
sleep 300 &
NEWPID=$!
```

**Confirm it is running:**
```bash
ps -p "$NEWPID" -o pid,cmd,stat
```

**Observation:**
- The new process should appear in the process list again.

---

## 14) Find the parent process ID (PPID) of a selected process and trace its process hierarchy

**Choose a process:**
```bash
ps -ef | head
```

**Show PPID:**
```bash
ps -o pid,ppid,cmd -p <PID>
```

**Trace hierarchy:**
```bash
pstree -p
```

or for one process:
```bash
pstree -ps <PID>
```

**Observation:**
- PPID shows the parent of a process.
- `pstree` displays the process tree.

---

## 15) Identify the top five processes consuming the most system resources and document their details

**CPU-heavy processes:**
```bash
ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head -n 5
```

**Memory-heavy processes:**
```bash
ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%mem | head -n 5
```

**Observation:**
- Record PID, PPID, command, CPU%, and MEM%.
- You can include the output in your submission.

---

## 16) Run a process with modified priority and compare its CPU usage with normal processes

**Start a low-priority process:**
```bash
nice -n 10 yes > /dev/null &
LOWPID=$!
```

**Check its priority:**
```bash
ps -o pid,ni,pri,cmd -p "$LOWPID"
```

**Compare with normal priority process:**
```bash
yes > /dev/null &
NORMALPID=$!
ps -o pid,ni,pri,cmd -p "$NORMALPID" "$LOWPID"
```

**Stop both:**
```bash
kill "$LOWPID" "$NORMALPID"
```

**Observation:**
- Lower priority means the scheduler may give it less CPU preference.

---

## 17) Monitor system resource usage continuously while performing multiple tasks in different terminals

**Terminal 1:**
```bash
top
```

**Terminal 2:**
```bash
watch -n 2 free -h
```

**Terminal 3:**
```bash
watch -n 2 'ps -eo pid,cmd,%cpu,%mem --sort=-%cpu | head -n 8'
```

**Observation:**
- Compare CPU and memory changes while launching and stopping processes.

---

## 18) Simulate a runaway process scenario and isolate the problematic process without affecting other services

**Create runaway-like process:**
```bash
yes > /dev/null &
RUNPID=$!
```

**Identify it:**
```bash
ps -p "$RUNPID" -o pid,cmd,%cpu
```

**Stop only that process:**
```bash
kill "$RUNPID"
```

If needed:
```bash
kill -9 "$RUNPID"
```

**Observation:**
- Only the problematic PID is terminated.
- Other system processes remain unaffected.

---

## 19) Analyze system performance during high load and identify whether CPU or memory is the bottleneck

**Monitor CPU and memory together:**
```bash
top
free -h
vmstat 1 5
```

**Observation guide:**
- High `%us` / `%sy` in `top` indicates CPU pressure.
- Low available memory and swapping indicate memory pressure.
- High `wa` in `top` or `vmstat` may indicate I/O wait.

**Conclusion example:**
- “The bottleneck was CPU because CPU usage stayed high while memory remained stable.”
- Or:
- “The bottleneck was memory because available memory dropped significantly and swap activity increased.”

---

## 20) Perform basic system tuning to reduce resource consumption and verify the improvement through monitoring tools

**Safe tuning steps:**
```bash
# reduce number of unnecessary background jobs
jobs -l
kill %1   # use only for jobs started by you

# stop CPU test processes
pkill yes

# check disk and memory status
free -h
uptime
```

**Optional cleanup of cache (only if you understand it and have permission):**
```bash
sync
```

**Verify improvement:**
```bash
top
free -h
uptime
```

**Observation:**
- After stopping heavy test processes, CPU usage and load should decrease.
- Available memory should stabilize.

---

## Final Submission Notes
Include the following in your submission:
- Command outputs or screenshots
- Process IDs where required
- Short observations for each task
- Any differences you saw on your system compared to the examples

## Conclusion
This assignment demonstrated process management, job control, signals, CPU monitoring, memory monitoring, priority handling, and performance analysis using Linux terminal tools.
