# Week 4 — Permissions, Users, and Packages

This week is about answering three big questions: **who am I, what am I allowed to do, and how do I install things?** Permissions and `sudo` are where Linux starts feeling like a real multi-user system. Package managers are where the terminal stops being a toy and becomes their primary way to install software.

---

## Setup — Create Sample Files First

Have the learner create a `week4` practice folder with files of different permission states. Copy-paste this block into the terminal:

```
mkdir -p ~/week4 && cd ~/week4

# A plain text file
echo "hello from a regular file" > notes.txt

# A "secret" file we'll lock down later
echo "this is private" > secret.txt

# A simple shell script (not yet executable)
cat > hello.sh << 'EOF'
#!/bin/bash
echo "Hello from a script!"
EOF

# A shared folder where we'll experiment with group access
mkdir shared
echo "team file" > shared/team.txt

# A folder with mixed contents for permission demos
mkdir mixed
touch mixed/public.txt mixed/private.txt mixed/script.sh
```

Now all the tasks below will work as expected. All commands assume you're in `~/week4` unless stated otherwise.

---

## Commands to Cover

### Who Am I?
- `whoami` — show the current username
- `id` — show user ID, group ID, and group memberships
- `groups` — list groups the current user belongs to
- `who` — list users currently logged into the system
- `w` — like `who` but with more detail about what they're doing
- `hostname` — show the computer's name

### Becoming the Superuser
- `sudo <command>` — run a single command as root
- `sudo !!` — re-run the last command with `sudo` (huge time-saver)
- `sudo -i` — open a root shell (use sparingly)
- `exit` — leave the root shell
- `passwd` — change your own password
- `sudo passwd <username>` — change another user's password

### Reading Permissions
- `ls -l` — long listing shows permissions like `-rw-r--r--`
- `ls -la` — same, including hidden files
- `stat file.txt` — detailed info: permissions, owner, size, timestamps

The permission string breaks down as:
- First character: `-` for file, `d` for directory, `l` for symlink
- Next 3: **owner** permissions (read, write, execute)
- Next 3: **group** permissions
- Last 3: **everyone else** permissions

### Changing Permissions
- `chmod +x file.sh` — make executable
- `chmod -x file.sh` — remove executable
- `chmod +r file.txt` — make readable
- `chmod -w file.txt` — remove write permission
- `chmod 644 file.txt` — owner read/write, others read only (most common for files)
- `chmod 755 script.sh` — owner full, others read/execute (most common for scripts)
- `chmod 600 secret.txt` — only owner can read/write
- `chmod -R 755 foldername` — apply recursively to a folder

The number format (a quick mental cheat sheet):
- `4` = read, `2` = write, `1` = execute
- Add them up: `7` = rwx, `6` = rw-, `5` = r-x, `4` = r--

### Changing Ownership
- `sudo chown username file.txt` — change owner
- `sudo chown username:groupname file.txt` — change owner and group
- `sudo chgrp groupname file.txt` — change just the group
- `sudo chown -R username foldername` — apply recursively

### Users and Groups (Read-Only for Beginners)
- `cat /etc/passwd` — list all users on the system
- `cat /etc/group` — list all groups
- `sudo adduser newname` — create a new user (just show, don't always do)
- `sudo deluser newname` — remove a user
- `sudo usermod -aG groupname username` — add a user to a group

### Package Management (Ubuntu/Debian)
- `sudo apt update` — refresh the list of available packages
- `sudo apt upgrade` — install available updates
- `sudo apt install <package>` — install software
- `sudo apt remove <package>` — uninstall software
- `sudo apt autoremove` — clean up unused dependencies
- `apt search <keyword>` — search for a package
- `apt show <package>` — info about a package
- `dpkg -l` — list installed packages
- `dpkg -l | grep <name>` — check if something specific is installed

### Other Distros (Briefly Mention)
- Fedora/RHEL: `dnf install`, `dnf remove`, `dnf update`
- Arch: `pacman -S`, `pacman -R`, `pacman -Syu`
- The concept is the same everywhere — just different command names.

---

## Small Tasks

Give these one by one. Each takes 5–15 minutes.

### 1. Know Thyself
- Run `whoami` — what's your username?
- Run `id` — read out your user ID and group IDs
- Run `groups` — what groups do you belong to?
- Run `hostname` — what's your machine called?

### 2. Read the Permission String
Run `ls -l` in `~/week4`. Pick one file and decode its permission string out loud:
- Is it a file or a directory?
- Can the owner read it? Write it? Execute it?
- Can the group? Can everyone else?

Do this for `notes.txt`, `hello.sh`, and `shared`. Notice the directory has different default permissions than the files.

### 3. Make a Script Run
- Try running the script directly: `./hello.sh`
- It should fail with "Permission denied"
- Make it executable: `chmod +x hello.sh`
- Try again: `./hello.sh`
- Talk about why scripts aren't executable by default — it's a safety feature.

### 4. Lock Down the Secret
- Run `ls -l secret.txt` — note the current permissions
- Lock it down so only you can read it: `chmod 600 secret.txt`
- Run `ls -l secret.txt` again — should now show `-rw-------`
- Try to read it: `cat secret.txt` (works, you're the owner)
- Explain that another user on the system would be blocked now.

### 5. The Number Game
Without running anything yet, predict what these commands will produce in `ls -l`:
- `chmod 644 notes.txt` → ?
- `chmod 755 hello.sh` → ?
- `chmod 700 secret.txt` → ?

Now run them and check. This is the fastest way to internalize the octal numbers.

### 6. Sudo and the Magic Bang Bang
- Try: `apt update` (without sudo) — it should fail
- Run `sudo !!` — this re-runs the last command with `sudo`
- Watch it succeed
- Explain: `!!` is shorthand for "the last command I typed." Huge time-saver.

### 7. The User Roster
- Run `cat /etc/passwd` and scroll through it
- Each line is a user — most are system accounts, not real people
- Find your own username in the list
- The fields are: `username:x:UID:GID:fullname:home_dir:shell` — explain each briefly

### 8. Install Something Useful
Walk through a full package install cycle:
- `sudo apt update` — refresh
- `apt search htop` — see if it's available
- `sudo apt install htop` — install it
- Run `htop` — a nicer version of `top`, press `q` to quit
- Try installing `tldr` too: `sudo apt install tldr`
- Now run `tldr ls` or `tldr grep` — beginner-friendly command examples

### 9. Search Before You Install
Use `apt search` to find:
- A text editor (try `apt search editor`)
- A music player (try `apt search music player`)
- A file manager (try `apt search file manager`)

The point: the Linux software world is huge, and `apt search` is the gateway.

### 10. The Permission Puzzle
Set permissions on the files in the `mixed` folder to match this scenario:
- `public.txt` — anyone can read, only you can write
- `private.txt` — only you can read or write
- `script.sh` — only you can read, write, and execute it

Use `chmod` with octal numbers. Verify with `ls -l mixed/`. If they get it right:
- `public.txt` → `644`
- `private.txt` → `600`
- `script.sh` → `700`

### 11. Real-World Cleanup
- Run `sudo apt autoremove` to clean up unused dependencies
- Run `dpkg -l | wc -l` to see how many packages are installed (it's a lot)
- Run `dpkg -l | grep python` to see all python-related packages

This shows that a Linux system is built from hundreds of small pieces, all managed by `apt`.

---

## Teaching Notes

- **Sudo is power. Treat it like power.** Don't get casual about it. The classic warning from the first time you use `sudo` says it well — "with great power comes great responsibility."
- The most common beginner mistake: running `chmod 777` on everything because it makes errors go away. Push back hard on this. `777` means "everyone can do everything" and is almost always wrong.
- When teaching the octal numbers, draw the `rwx | rwx | rwx` grid on paper. Visual learners get it instantly.
- The package manager feels magical compared to Windows/Mac — no browser, no installer, no "next next finish." Lean into that moment.
- If something breaks during `apt install`, run `sudo apt --fix-broken install` first before panicking.
- Skip `adduser` and `usermod` as hands-on tasks unless they specifically need multi-user. Just show that they exist.
- By the end of this week, they should feel like they **own** their system rather than being a guest in it.
