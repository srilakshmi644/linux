# Week 5 — Processes, Pipes, and Putting It Together

This is the week everything clicks. Running programs, watching them run, stopping them, and — most importantly — **chaining commands together** so the terminal becomes a power tool, not just a typing exercise. By the end of this week they should be writing tiny commands that do real work.

---

## Setup — Create Sample Files First

Have the learner create a `week5` practice folder with files that have realistic content for piping, filtering, and counting. Copy-paste this into the terminal:

```
mkdir -p ~/week5 && cd ~/week5

# A list of fake server log entries
cat > server.log << 'EOF'
2026-05-10 09:01:12 INFO  user alice logged in
2026-05-10 09:02:45 INFO  user bob logged in
2026-05-10 09:05:01 ERROR database connection failed
2026-05-10 09:05:02 ERROR retrying database connection
2026-05-10 09:05:10 INFO  database connection restored
2026-05-10 09:07:33 WARN  disk usage at 82%
2026-05-10 09:10:15 INFO  user alice logged out
2026-05-10 09:11:00 ERROR failed login attempt for user carol
2026-05-10 09:12:22 INFO  user dave logged in
2026-05-10 09:15:44 WARN  high memory usage
2026-05-10 09:20:01 ERROR database timeout
2026-05-10 09:25:00 INFO  user bob logged out
EOF

# A list of names with some duplicates
cat > names.txt << 'EOF'
alice
bob
carol
alice
dave
bob
eve
alice
frank
EOF

# A CSV-ish file of fruit and prices
cat > fruits.txt << 'EOF'
apple 1.20
banana 0.50
cherry 3.00
apple 1.10
banana 0.55
date 4.50
apple 1.25
EOF

# A simple background script for process practice
cat > slow.sh << 'EOF'
#!/bin/bash
echo "starting slow job..."
sleep 60
echo "done"
EOF
chmod +x slow.sh
```

All commands below assume you're in `~/week5`.

---

## Commands to Cover

### Watching Processes
- `ps` — list your own processes
- `ps aux` — list **every** process on the system (the most common form)
- `ps aux | grep firefox` — find a specific process
- `top` — live process monitor (press `q` to quit)
- `htop` — nicer version of `top` (install with `sudo apt install htop`)
- `pgrep firefox` — get the PID of a process by name
- `pidof firefox` — same idea, slightly different output

### Controlling Processes
- `kill <PID>` — politely ask a process to stop
- `kill -9 <PID>` — force a process to stop (last resort)
- `killall firefox` — kill all processes by that name
- `pkill firefox` — same idea using pattern matching
- `Ctrl + C` — stop the program running in the foreground
- `Ctrl + Z` — suspend (pause) the program
- `bg` — resume a suspended program in the **background**
- `fg` — bring a background program back to the **foreground**
- `jobs` — list jobs you've backgrounded
- `command &` — start a command directly in the background

### Pipes — The Big Idea
A pipe (`|`) takes the **output** of one command and feeds it as the **input** of the next.
- `ls | wc -l` — count files in the current folder
- `cat server.log | grep ERROR` — pull only ERROR lines from the log
- `ps aux | grep python` — find all running python processes

### Redirection (Recap from Week 3, Used Heavily Here)
- `command > file` — save output to a file (overwrite)
- `command >> file` — save output to a file (append)
- `command < file` — feed a file as input to a command
- `command 2> errors.log` — redirect **error** output to a file
- `command > out.log 2>&1` — capture both normal and error output

### Useful Tools for Pipelines
- `sort` — sort lines alphabetically
- `sort -n` — sort numerically
- `sort -r` — reverse sort
- `uniq` — remove **adjacent** duplicate lines (often paired with `sort`)
- `uniq -c` — count how many times each line appeared
- `cut -d' ' -f1` — pick out the 1st field, using space as the separator
- `cut -d',' -f2` — pick out the 2nd field from a CSV
- `awk '{print $1}'` — print the 1st column (more powerful than `cut`)
- `awk '{print $1, $3}'` — print the 1st and 3rd columns
- `sed 's/old/new/g'` — find and replace text
- `tr 'a-z' 'A-Z'` — translate lowercase to uppercase
- `xargs` — feed output as **arguments** to the next command

### System Snapshot Commands
- `df -h` — disk space (human-readable)
- `du -sh foldername` — size of a folder
- `du -sh *` — size of every item in current folder
- `free -h` — memory usage
- `uptime` — how long the system has been running
- `uname -a` — kernel and system info

---

## Small Tasks

Give these one by one. Each takes 5–15 minutes.

### 1. Spy on Yourself
- Run `ps` — what's running in your terminal right now?
- Run `ps aux | wc -l` — how many processes are running on the whole system?
- Run `top`, watch it update, press `q` to quit
- If `htop` is installed, run it instead — it's prettier and easier to read

### 2. Find a Process by Name
- Open another terminal and run `sleep 300 &` to start a 5-minute sleep in the background
- In the first terminal, run `ps aux | grep sleep` — there it is
- Note the **PID** (the number in the second column)
- Kill it with `kill <PID>`
- Confirm it's gone with `ps aux | grep sleep`

### 3. The Background Job Dance
- Run `./slow.sh` — it takes 60 seconds
- Press `Ctrl + Z` to suspend it
- Type `jobs` — see your suspended job
- Type `bg` — it resumes in the background
- Type `fg` — it comes back to the foreground
- Press `Ctrl + C` to kill it

This is the single most useful workflow trick in the terminal. Practice it twice.

### 4. The First Real Pipe
- Count how many lines are in the log: `wc -l server.log`
- Now use a pipe to do the same: `cat server.log | wc -l`
- Talk about how the second form is more flexible — you can stick more steps in between

### 5. Find All the Errors
- Show only ERROR lines: `grep ERROR server.log`
- Count them: `grep ERROR server.log | wc -l`
- Show only WARN lines: `grep WARN server.log`
- Show everything that's **not** INFO: `grep -v INFO server.log`

### 6. The Classic Sort-Uniq Combo
Using `names.txt`:
- Show duplicates clearly: `sort names.txt`
- Remove duplicates: `sort names.txt | uniq`
- Count each name's appearances: `sort names.txt | uniq -c`
- Find the most popular name: `sort names.txt | uniq -c | sort -rn | head -n 1`

That last command is **5 tools chained together**. Walk through it left-to-right:
1. sort the names
2. count duplicates
3. sort the counts numerically, biggest first
4. take just the top line

This is the moment most beginners realize how powerful pipes are.

### 7. Cut and Awk
Using `fruits.txt`:
- Pick out just the fruit names: `cut -d' ' -f1 fruits.txt`
- Same thing with awk: `awk '{print $1}' fruits.txt`
- Pick out just the prices: `awk '{print $2}' fruits.txt`
- Get a sorted list of unique fruits: `awk '{print $1}' fruits.txt | sort | uniq`

### 8. Find and Replace
- Replace "apple" with "APPLE" in the output: `sed 's/apple/APPLE/g' fruits.txt`
- Convert the whole file to uppercase: `tr 'a-z' 'A-Z' < fruits.txt`
- Note that these print to the screen — the original file is unchanged. To save, redirect: `sed 's/apple/APPLE/g' fruits.txt > fruits-loud.txt`

### 9. Top 3 Error Sources
A small detective challenge. From `server.log`:
- Extract just the log level (the 3rd field): `awk '{print $3}' server.log`
- Sort and count each level: `awk '{print $3}' server.log | sort | uniq -c`
- Sort by frequency: `awk '{print $3}' server.log | sort | uniq -c | sort -rn`

They've just built a one-line log analyzer.

### 10. Disk Detective
- See total disk space: `df -h`
- See size of home folder: `du -sh ~`
- See size of every folder in home: `du -sh ~/*`
- Find the biggest folder: `du -sh ~/* | sort -rh | head -n 5`

Useful for real life — finding what's eating your disk.

### 11. Save Output for Later
- Save the list of errors to a file: `grep ERROR server.log > errors.txt`
- Append a header: `echo "--- Errors found ---" >> report.txt && grep ERROR server.log >> report.txt`
- Read it back: `cat report.txt`

This is how shell scripts start to feel useful — they generate real artifacts.

### 12. The Capstone — One-Liner Report
End the week with this challenge. Build the command step by step, adding one stage at a time:

```
cat server.log | grep -v INFO | awk '{print $3}' | sort | uniq -c | sort -rn > problems.txt
```

Then have them `cat problems.txt`. Walk through what each piped step is doing. They've just built a real, useful data pipeline.

---

## Teaching Notes

- **Pipes are the soul of Unix.** Every Linux power user thinks in pipes. Drill the metaphor: each command does one small thing well, and pipes connect them like water flowing through hoses.
- When chaining many commands, encourage building **left to right, one step at a time**. Run after each pipe to confirm the output looks right before adding the next stage.
- The first time they use `Ctrl + C` to escape a runaway command, point it out. That muscle memory saves them daily for the rest of their life.
- `kill -9` should be a last resort, not a default. Plain `kill` lets the program clean up first. Explain the difference once and they'll get it.
- `awk` and `sed` are entire languages — don't go deep this week. Just `awk '{print $N}'` and `sed 's/x/y/g'` are enough to be dangerous.
- If they ask "should I learn shell scripting next?" — yes, but only after these pipes feel natural. Shell scripts are just saved-up pipe chains with a few extras.
- Celebrate the capstone task. Building a one-line log analyzer from scratch is a real achievement, and it earns them the right to say **"I know Linux."**
