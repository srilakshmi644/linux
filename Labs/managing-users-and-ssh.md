# Lab — Managing Users, Passwords, and SSH Login

A hands-on lab that teaches how Linux handles **identity** — who a user is, how they prove it, and how they log in from another machine. By the end they'll have created real users, set passwords, configured `sudo`, and logged in over SSH using both passwords and keys.

**Estimated time:** 2–3 hours.

**Format:** Each step is broken into numbered sub-steps. Every command shows the **expected output** so the learner can self-check.

**Prerequisites:** Weeks 1–5. Especially Week 4 (permissions and sudo) and Week 3 (vi).

---

## What This Lab Covers

| Skill | Where It's Used |
|-------|-----------------|
| Creating and removing users | Steps 2, 3, 8 |
| Setting and changing passwords | Steps 3, 4 |
| Groups and `sudo` access | Step 5 |
| Reading `/etc/passwd`, `/etc/shadow`, `/etc/group` | Step 6 |
| SSH server setup and status | Step 7 |
| Password-based SSH login | Step 8 |
| SSH key generation and passwordless login | Steps 9, 10 |
| Locking down SSH (disabling password login) | Step 11 |

---

## Step 1 — Confirm Your Starting Point

**1.1** Check who you are.
```
whoami
```
*Expected output:* Your own username (e.g., `alice`).

**1.2** Check your user ID and groups.
```
id
```
*Expected output:*
```
uid=1000(alice) gid=1000(alice) groups=1000(alice),4(adm),27(sudo),...
```
Note whether `sudo` appears in your groups. You'll need it.

**1.3** Confirm sudo works.
```
sudo whoami
```
*Expected output:* `root` (after entering your password).

If sudo doesn't work, stop and fix that before continuing.

---

## Step 2 — Create a New User

**2.1** Create a user called `devuser`.
```
sudo adduser devuser
```
*Expected output:* A series of prompts:
```
Adding user `devuser' ...
Adding new group `devuser' (1001) ...
Adding new user `devuser' (1001) with group `devuser' ...
Creating home directory `/home/devuser' ...
Copying files from `/etc/skel' ...
New password:
```
When it asks for a password, type one you'll remember (e.g., `devpass123`). Type it twice. Press Enter to skip the "Full Name", "Room Number", etc. Answer `Y` at the end.

**2.2** Confirm the user exists.
```
getent passwd devuser
```
*Expected output:*
```
devuser:x:1001:1001:,,,:/home/devuser:/bin/bash
```
The fields are: `username:x:UID:GID:full_name:home_dir:shell`.

**2.3** Confirm their home folder was created.
```
ls -la /home/devuser
```
*Expected output:*
```
total 20
drwxr-x--- 2 devuser devuser 4096 May 10 09:00 .
drwxr-xr-x 4 root    root    4096 May 10 09:00 ..
-rw-r--r-- 1 devuser devuser  220 May 10 09:00 .bash_logout
-rw-r--r-- 1 devuser devuser 3771 May 10 09:00 .bashrc
-rw-r--r-- 1 devuser devuser  807 May 10 09:00 .profile
```
Note the ownership: `devuser devuser`.

---

## Step 3 — Switch to the New User

**3.1** Become `devuser`.
```
su - devuser
```
*Expected output:* Password prompt. Enter `devpass123`. On success, the prompt changes to something like `devuser@yourmachine:~$`.

**3.2** Confirm the identity switch.
```
whoami
```
*Expected output:* `devuser`

**3.3** Check where you are.
```
pwd
```
*Expected output:* `/home/devuser`

**3.4** Try running sudo.
```
sudo whoami
```
*Expected output:*
```
devuser is not in the sudoers file.  This incident will be reported.
```
Good — `devuser` doesn't have admin access yet. We'll fix that in Step 5.

**3.5** Return to your own user.
```
exit
```
*Expected output:* `logout` and the prompt returns to your original username.

---

## Step 4 — Change Passwords

**4.1** Change your own password.
```
passwd
```
*Expected output:* Prompts for your current password, then twice for a new one. Enter something you'll remember. On success:
```
passwd: password updated successfully
```

**4.2** As root, change `devuser`'s password without knowing the old one.
```
sudo passwd devuser
```
*Expected output:*
```
New password:
Retype new password:
passwd: password updated successfully
```
Set it to something new like `newdevpass456`.

**4.3** Confirm the new password works by switching to devuser.
```
su - devuser
```
Enter `newdevpass456`. If you get in, the change worked.

**4.4** Return.
```
exit
```

---

## Step 5 — Grant Sudo Access

**5.1** Check `devuser`'s current groups.
```
groups devuser
```
*Expected output:*
```
devuser : devuser
```
Only in their own group. No `sudo`.

**5.2** Add `devuser` to the `sudo` group.
```
sudo usermod -aG sudo devuser
```
*Expected output:* No output.

The `-aG` means "append to Group" — critical. If you use `-G` alone, it **replaces** all groups and can lock users out. Never forget the `a`.

**5.3** Confirm the change.
```
groups devuser
```
*Expected output:*
```
devuser : devuser sudo
```

**5.4** Switch to devuser and test sudo.
```
su - devuser
sudo whoami
```
*Expected output:* Password prompt (enter `newdevpass456`), then `root`.

**5.5** Return to your own user.
```
exit
```

---

## Step 6 — Where Users Live in the Filesystem

Everything about users is stored in text files under `/etc`. This is the moment to appreciate that even user accounts are just text.

**6.1** Look at the user database.
```
grep devuser /etc/passwd
```
*Expected output:*
```
devuser:x:1001:1001:,,,:/home/devuser:/bin/bash
```

**6.2** Look at the group database.
```
grep sudo /etc/group
```
*Expected output:*
```
sudo:x:27:alice,devuser
```
(Both usernames will be there — yours and devuser's.)

**6.3** Look at the shadow file (password hashes). This one requires sudo.
```
sudo grep devuser /etc/shadow
```
*Expected output:*
```
devuser:$6$xyzabc...$hashedstuff...:19850:0:99999:7:::
```
The `$6$...` is the hashed password. Explain: **Linux never stores passwords in plain text.** It stores a one-way hash and compares hashes at login time.

**6.4** Count how many real (non-system) users exist.
```
awk -F: '$3 >= 1000 && $3 < 65534 {print $1}' /etc/passwd
```
*Expected output:* Just the human users on the system, usually 2 or 3 names.

That was a Week 5 pipe applied to a real admin question.

---

## Step 7 — Install and Check SSH

Now we set up the way users log in from another machine.

**7.1** Install the SSH server.
```
sudo apt update
sudo apt install openssh-server -y
```
*Expected output:* Package install text ending without errors.

**7.2** Check the SSH service status.
```
sudo systemctl status ssh
```
*Expected output:* A block containing `active (running)` in green. Press `q` to exit.

**7.3** Confirm SSH is listening on port 22.
```
sudo ss -tlnp | grep ssh
```
*Expected output:*
```
LISTEN 0 128 0.0.0.0:22 0.0.0.0:* users:(("sshd",pid=...))
LISTEN 0 128 [::]:22    [::]:*    users:(("sshd",pid=...))
```

**7.4** Find your machine's IP address.
```
ip addr | grep "inet " | grep -v 127.0.0.1
```
*Expected output:*
```
inet 192.168.1.42/24 brd 192.168.1.255 scope global dynamic ...
```
Note the IP address (yours will differ) — you'll use it in a moment.

---

## Step 8 — Log In Over SSH with a Password

**8.1** SSH into your own machine as `devuser` (this is called a loopback SSH).
```
ssh devuser@localhost
```
*Expected output:* First-time prompt:
```
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ED25519 key fingerprint is SHA256:...
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```
Type `yes` and press Enter. Then it prompts for `devuser`'s password. Enter `newdevpass456`.

On success the prompt changes to `devuser@yourmachine:~$`.

**8.2** Confirm you're logged in as devuser via SSH.
```
whoami
who
```
*Expected output for `whoami`:* `devuser`

*Expected output for `who`:* A list showing at least two logins — your original session and the new SSH one. Something like:
```
alice    tty1         2026-05-10 09:00
devuser  pts/1        2026-05-10 09:30 (127.0.0.1)
```

**8.3** Log out of the SSH session.
```
exit
```
*Expected output:* `logout` and then `Connection to localhost closed.` Prompt returns to your original user.

---

## Step 9 — Generate an SSH Key Pair

Passwords are fine, but SSH keys are safer and don't require typing a password every time. Every real Linux user has one.

**9.1** Check if you already have a key.
```
ls ~/.ssh/
```
*Expected output:* Either `No such file or directory` (fine, we'll create one) or a list including `id_rsa` and `id_rsa.pub` (already have one — skip to 9.3).

**9.2** Generate a new key pair.
```
ssh-keygen -t ed25519 -C "linux-lab-key"
```
*Expected output:* Prompts:
```
Enter file in which to save the key (/home/alice/.ssh/id_ed25519):
```
Press Enter to accept the default. Then:
```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
For this lab, press Enter twice to skip the passphrase. In real life, use one.

Finally:
```
Your identification has been saved in /home/alice/.ssh/id_ed25519
Your public key has been saved in /home/alice/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xyz... linux-lab-key
```

**9.3** Look at the two files that were created.
```
ls -l ~/.ssh/
```
*Expected output:*
```
-rw------- 1 alice alice  464 May 10 09:35 id_ed25519
-rw-r--r-- 1 alice alice  100 May 10 09:35 id_ed25519.pub
known_hosts
```

The **private key** (`id_ed25519`) has `-rw-------` permissions — only you can read it. If it ever has looser permissions, SSH will refuse to use it.

**9.4** Look at the public key. This is safe to share.
```
cat ~/.ssh/id_ed25519.pub
```
*Expected output:*
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIABC...xyz linux-lab-key
```

---

## Step 10 — Log In with the Key (Passwordless)

Now install your public key into devuser's account so you can SSH in without a password.

**10.1** Copy your key to devuser using `ssh-copy-id`.
```
ssh-copy-id devuser@localhost
```
*Expected output:* Prompts for devuser's password once, then:
```
Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'devuser@localhost'"
and check to make sure that only the key(s) you wanted were added.
```

**10.2** SSH in again — no password prompt this time.
```
ssh devuser@localhost
```
*Expected output:* You're logged in directly, no password requested. The prompt changes to `devuser@yourmachine:~$`.

That's the magic moment — cryptography just authenticated you.

**10.3** Look at what ssh-copy-id did on devuser's side.
```
cat ~/.ssh/authorized_keys
```
*Expected output:* Your public key from step 9.4 appears here. This file is how the server decides who's allowed in.

**10.4** Log out.
```
exit
```

---

## Step 11 — Lock Down SSH (Disable Password Login)

Now that keys work, disable password login for stronger security. This is what real production servers do.

**11.1** Open the SSH server config.
```
sudo vi /etc/ssh/sshd_config
```

**11.2** Search inside vi for `PasswordAuthentication`.

In normal mode, type:
```
/PasswordAuthentication
```
Press Enter. It jumps to the line. It probably reads `#PasswordAuthentication yes` (commented out) or `PasswordAuthentication yes`.

**11.3** Change the line to disable password login.

Press `i` to enter insert mode. Edit the line so it reads exactly:
```
PasswordAuthentication no
```
Make sure there's **no `#` at the start** (or it's ignored).

**11.4** Save and quit: press `Esc`, then `:wq`, then Enter.

**11.5** Test the config for syntax errors before restarting.
```
sudo sshd -t
```
*Expected output:* No output means no errors. If it complains, go back and fix the file.

**11.6** Restart SSH.
```
sudo systemctl restart ssh
```
*Expected output:* No output.

**11.7** Test — key login should still work.
```
ssh devuser@localhost
```
*Expected result:* Logs in without a password. Type `exit` to leave.

**11.8** Test — password login should now fail.

Temporarily rename your key to hide it, so SSH is forced to try password auth.
```
mv ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.hidden
mv ~/.ssh/id_ed25519.pub ~/.ssh/id_ed25519.pub.hidden
ssh devuser@localhost
```
*Expected output:*
```
devuser@localhost: Permission denied (publickey).
```
Passwords are dead. Only keys work now.

**11.9** Restore your key.
```
mv ~/.ssh/id_ed25519.hidden ~/.ssh/id_ed25519
mv ~/.ssh/id_ed25519.pub.hidden ~/.ssh/id_ed25519.pub
```

---

## Step 12 — Cleanup (Optional)

If you want to reset everything you created in this lab:

**12.1** Re-enable password login (undo Step 11).
```
sudo vi /etc/ssh/sshd_config
```
Change `PasswordAuthentication no` back to `PasswordAuthentication yes`. Save with `:wq`.

**12.2** Restart SSH.
```
sudo systemctl restart ssh
```

**12.3** Remove `devuser` from sudo.
```
sudo deluser devuser sudo
```
*Expected output:*
```
Removing user `devuser' from group `sudo' ...
Done.
```

**12.4** Delete the user entirely (including their home folder).
```
sudo deluser --remove-home devuser
```
*Expected output:* A few lines ending with `Done.`

**12.5** Confirm they're gone.
```
getent passwd devuser
```
*Expected output:* No output. The user is gone.

---

## Final Checklist

- ☐ Created a new user with `adduser`
- ☐ Switched identities with `su`
- ☐ Set and changed passwords
- ☐ Added a user to the `sudo` group with `usermod -aG`
- ☐ Read the user, group, and shadow files
- ☐ Installed and verified the SSH server
- ☐ Logged in over SSH with a password
- ☐ Generated an SSH key pair
- ☐ Deployed a public key with `ssh-copy-id`
- ☐ Logged in passwordless with SSH
- ☐ Locked down SSH by disabling password auth
- ☐ Removed a user cleanly

That's the full lifecycle of a user and their access on a Linux system.

---

## Stretch Goals (Optional)

1. **SSH from a phone.** Install Termux (Android) or Blink Shell (iOS). SSH into the machine over WiFi using the IP from Step 7.4.

2. **Custom SSH port.** Change the SSH port from 22 to something like 2222 by editing `Port 22` in `sshd_config`. Connect with `ssh -p 2222 devuser@localhost`.

3. **SSH config file shortcuts.** Create `~/.ssh/config` with an alias so you can type `ssh dev` instead of `ssh devuser@localhost`.

4. **Force key passphrase and use ssh-agent.** Regenerate your key with a passphrase, then set up `ssh-agent` so you type the passphrase once per session.

5. **Add a second user and test group access.** Create `devuser2`, put both users in a shared group like `developers`, create a folder they can both write to using `chmod g+w` and `chown :developers`.

---

## Teaching Notes

- **The `usermod -a` flag is critical.** Missing the `a` in `usermod -aG` will silently remove someone from all their other groups, including `sudo`. Have them internalize this one.
- **`/etc/passwd`, `/etc/shadow`, and `/etc/group` are just text files.** Reinforce the Week 3 lesson: even users are text. If they ever get truly stuck, they can boot into recovery mode and edit these directly.
- The **key-based login moment (Step 10.2)** is the reward. No password, just cryptography. Let them enjoy it.
- **Point out that everything they just did is what real cloud servers require.** AWS, DigitalOcean, and Linode all default to key-only SSH login. They just learned the industry standard.
- If they lose their private key, they're locked out. Reinforce **backing up `~/.ssh/`** somewhere safe.
- The lab uses `localhost` throughout so it works on a single machine. If they have two machines (or a cloud VM), have them repeat Steps 8–10 across real machines. It feels completely different when you're logging into a **real server** somewhere else in the world.
