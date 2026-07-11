# Capstone Project — Deploy a Static Website with Apache (httpd)

A hands-on project that pulls together **everything from Weeks 1–5**. By the end, they'll have a real website running on their machine that they can open in a browser.

**Estimated time:** 2–3 hours, best done in one sitting.

**Format:** Each step is broken into numbered sub-steps. For every command, the **expected output** is shown so the learner can self-check. If their output looks very different, stop and investigate before moving on.

---

## What This Project Covers

| Skill | Week | Where It's Used |
|-------|------|-----------------|
| Terminal orientation, `pwd`, `ls` | Week 1 | Every step |
| `cd`, `mkdir`, `cp`, `mv`, `rm` | Week 2 | Setting up files and folders |
| `vi`, `cat`, `less`, `grep`, `tail` | Week 3 | Editing config, reading logs |
| `sudo`, `chmod`, `chown`, `apt` | Week 4 | Installing Apache, fixing permissions |
| `ps`, `systemctl`, pipes, `curl` | Week 5 | Running and testing the server |

---

## Step 1 — Set Up a Project Folder

**1.1** Go home.
```
cd ~
```
*Expected output:* No output. The prompt returns.

**1.2** Confirm where you are.
```
pwd
```
*Expected output:* `/home/yourusername`

**1.3** Create the project folder with a content subfolder inside.
```
mkdir -p my-website/content
```
*Expected output:* No output.

**1.4** Enter the project folder.
```
cd my-website
```
*Expected output:* No output.

**1.5** List everything (including hidden files).
```
ls -la
```
*Expected output:*
```
total 12
drwxr-xr-x  3 you you 4096 May 10 09:00 .
drwxr-xr-x 20 you you 4096 May 10 09:00 ..
drwxr-xr-x  2 you you 4096 May 10 09:00 content
```

---

## Step 2 — Install Apache

**2.1** Refresh the package list.
```
sudo apt update
```
*Expected output:* Several lines starting with `Hit:`, `Get:`, or `Ign:`, ending with `Reading package lists... Done`. Might prompt for your password.

**2.2** Search for the Apache package to confirm it's available.
```
apt search apache2 2>/dev/null | head -n 5
```
*Expected output:* A few lines including one like `apache2/... Apache HTTP Server`.

**2.3** Install Apache.
```
sudo apt install apache2 -y
```
*Expected output:* A long block of text ending with `Setting up apache2 ...` and no error at the end.

**2.4** Confirm it's installed.
```
apache2 -v
```
*Expected output:*
```
Server version: Apache/2.4.xx (Ubuntu)
Server built:   ...
```

**2.5** Find where the binary lives.
```
which apache2
```
*Expected output:* `/usr/sbin/apache2`

---

## Step 3 — Confirm the Server Is Running

**3.1** Check the Apache process is alive.
```
ps aux | grep apache2 | grep -v grep
```
*Expected output:* Several lines showing `apache2` processes. If this is empty, the server isn't running — go to step 3.3.

**3.2** Check the service status.
```
sudo systemctl status apache2
```
*Expected output:* A block including a green `active (running)` line. Press `q` to exit.

**3.3** If not running, start it.
```
sudo systemctl start apache2
```
*Expected output:* No output on success.

**3.4** Fetch the homepage from the terminal.
```
curl -s http://localhost | head -n 5
```
*Expected output:* HTML lines like `<!DOCTYPE html>` and `<html ...>`.

**3.5** Open a browser and visit `http://localhost`.
*Expected result:* An "Apache2 Ubuntu Default Page" appears.

---

## Step 4 — Find the Website Files

**4.1** Go to Apache's web root.
```
cd /var/www/html
```
*Expected output:* No output.

**4.2** List what's there.
```
ls -la
```
*Expected output:*
```
total 12
drwxr-xr-x 2 root root  4096 May 10 09:00 .
drwxr-xr-x 3 root root  4096 May 10 09:00 ..
-rw-r--r-- 1 root root 10918 May 10 09:00 index.html
```
Note that the owner is `root`, not you. That will matter shortly.

**4.3** Peek at the first 20 lines of the file.
```
cat index.html | head -n 20
```
*Expected output:* Raw HTML starting with `<!DOCTYPE html>` — the same page the browser rendered.

---

## Step 5 — Fix the Permissions Problem

**5.1** Try to edit the file directly.
```
vi index.html
```
Press `i`, type a character, press `Esc`, then `:wq`.

*Expected output:* An error like `"index.html" E212: Can't open file for writing`. Press Enter, then `:q!` to force quit.

**5.2** Confirm the file is owned by root.
```
ls -l index.html
```
*Expected output:*
```
-rw-r--r-- 1 root root 10918 May 10 09:00 index.html
```
The `root root` in the middle is the owner and group.

**5.3** Take ownership of the whole web root.
```
sudo chown -R $USER:$USER /var/www/html
```
*Expected output:* No output on success.

**5.4** Confirm the change.
```
ls -l index.html
```
*Expected output:*
```
-rw-r--r-- 1 yourusername yourusername 10918 May 10 09:00 index.html
```

---

## Step 6 — Build the Website

**6.1** Back up the original before editing.
```
cp index.html index.html.backup
```
*Expected output:* No output.

**6.2** Confirm the backup exists.
```
ls
```
*Expected output:*
```
index.html  index.html.backup
```

**6.3** Open the file in vi.
```
vi index.html
```

**6.4** Clear the file. In normal mode press `gg` (top of file), then `dG` (delete to end).
*Expected result:* The file is now empty. Only `~` characters show on the left.

**6.5** Press `i` to enter insert mode. You should see `-- INSERT --` at the bottom. Paste this content:
```html
<!DOCTYPE html>
<html>
<head>
  <title>About Me</title>
  <style>
    body { font-family: sans-serif; max-width: 640px; margin: 40px auto; padding: 0 20px; }
    h1 { color: #2a5db0; }
    .fact { padding: 8px; background: #f4f4f4; margin: 8px 0; border-radius: 4px; }
  </style>
</head>
<body>
  <h1>Hello, I'm Learning Linux!</h1>
  <p>I built and deployed this page from the terminal.</p>
  <div class="fact">I know how to use vi.</div>
  <div class="fact">I know what chmod means.</div>
  <div class="fact">I can pipe commands together.</div>
  <p><a href="about.html">More about me →</a></p>
</body>
</html>
```

**6.6** Save and quit. Press `Esc`, then type `:wq` and press Enter.
*Expected output:* Back at the shell prompt.

**6.7** Verify what was saved.
```
head -n 3 index.html
```
*Expected output:*
```
<!DOCTYPE html>
<html>
<head>
```

**6.8** Refresh `http://localhost` in the browser.
*Expected result:* The new "Hello, I'm Learning Linux!" page appears with a blue heading and three grey fact boxes.

---

## Step 7 — Add a Second Page

**7.1** Create the About page.
```
vi about.html
```

**7.2** Press `i` and enter this content:
```html
<!DOCTYPE html>
<html>
<head><title>About Me</title></head>
<body>
  <h1>A Bit More About Me</h1>
  <p>I started learning Linux weeks ago, and now I'm running a webserver.</p>
  <p><a href="index.html">← Back home</a></p>
</body>
</html>
```

**7.3** Save and quit: `Esc` then `:wq`.

**7.4** List the folder.
```
ls -la
```
*Expected output:* Three files: `index.html`, `index.html.backup`, `about.html`.

**7.5** Visit `http://localhost/about.html` in the browser and click the link back to home.
*Expected result:* Both pages load and links work.

---

## Step 8 — Watch the Server Logs Live

**8.1** Open a second terminal (keep the first one nearby). In the second terminal run:
```
sudo tail -f /var/log/apache2/access.log
```
*Expected output:* Might be empty at first, or a few log lines.

**8.2** Refresh the site in the browser a few times. Click between the two pages.
*Expected result:* New lines appear in the log terminal, one per request. Each line looks like:
```
127.0.0.1 - - [10/May/2026:09:15:03 +0000] "GET /index.html HTTP/1.1" 200 512 "-" "Mozilla/5.0 ..."
```

**8.3** Press `Ctrl + C` in the log terminal to stop following.

**8.4** Count total requests in the log.
```
sudo cat /var/log/apache2/access.log | wc -l
```
*Expected output:* A number like `12` (however many requests you made).

**8.5** Count requests for the about page specifically.
```
sudo cat /var/log/apache2/access.log | grep "about.html" | wc -l
```
*Expected output:* A small number — however many times you visited about.html.

**8.6** Show a summary of requests per IP.
```
sudo cat /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c
```
*Expected output:*
```
     12 127.0.0.1
```
(All requests came from your own machine, so the IP is `127.0.0.1`.)

---

## Step 9 — Break It and Fix It

**9.1** Stop Apache.
```
sudo systemctl stop apache2
```
*Expected output:* No output.

**9.2** Try to fetch the page.
```
curl http://localhost
```
*Expected output:* `curl: (7) Failed to connect to localhost port 80: Connection refused`.

**9.3** Confirm no Apache processes are running.
```
ps aux | grep apache2 | grep -v grep
```
*Expected output:* No output (empty).

**9.4** Check the error log for context.
```
sudo tail -n 10 /var/log/apache2/error.log
```
*Expected output:* Recent log lines — often something like `AH00169: caught SIGTERM, shutting down`.

**9.5** Start Apache again.
```
sudo systemctl start apache2
```
*Expected output:* No output.

**9.6** Verify it's alive.
```
curl -s http://localhost | head -n 3
```
*Expected output:*
```
<!DOCTYPE html>
<html>
<head>
```

---

## Step 10 — The Deployment Script

**10.1** Go back to the project folder.
```
cd ~/my-website
```

**10.2** Create the deploy script.
```
vi deploy.sh
```

**10.3** Press `i` and paste:
```bash
#!/bin/bash
echo "Deploying website at $(date)..."
cp content/*.html /var/www/html/
echo "Files deployed:"
ls -la /var/www/html/*.html
echo "Server status:"
systemctl is-active apache2
echo "Done."
```

**10.4** Save and quit: `Esc` then `:wq`.

**10.5** Make the script executable.
```
chmod +x deploy.sh
```
*Expected output:* No output.

**10.6** Confirm the permission change.
```
ls -l deploy.sh
```
*Expected output:*
```
-rwxr-xr-x 1 you you 240 May 10 09:30 deploy.sh
```
The `x` characters mean it's executable.

**10.7** Grab the current live pages into your project folder so the script has content to deploy.
```
cp /var/www/html/index.html content/
cp /var/www/html/about.html content/
ls content/
```
*Expected output:*
```
about.html  index.html
```

**10.8** Make a visible change in the content folder version so you can tell it deployed.
```
vi content/index.html
```
Change the heading text (e.g. "Hello, I'm Learning Linux!" → "Version 2 — Deployed by Script!"). Save with `:wq`.

**10.9** Run the deploy script.
```
./deploy.sh
```
*Expected output:*
```
Deploying website at Sat May 10 09:35:12 UTC 2026...
Files deployed:
-rw-r--r-- 1 you you  456 May 10 09:35 /var/www/html/about.html
-rw-r--r-- 1 you you  812 May 10 09:35 /var/www/html/index.html
Server status:
active
Done.
```

**10.10** Refresh the browser at `http://localhost`.
*Expected result:* The new heading text appears. **You just deployed a website with one command.**

---

## Final Checklist

By the end of this project they've done all of:

- ☐ Installed system software with `apt`
- ☐ Used `sudo` responsibly
- ☐ Changed file ownership with `chown`
- ☐ Made a file executable with `chmod +x`
- ☐ Edited files with `vi` (open, insert, save, quit)
- ☐ Navigated the filesystem with `cd`, `ls`, `pwd`
- ☐ Copied files with `cp`
- ☐ Watched a running process with `ps`
- ☐ Followed live logs with `tail -f`
- ☐ Chained commands with pipes (`|`)
- ☐ Used `grep`, `awk`, `sort`, `uniq`, `wc` on real data
- ☐ Tested a live web server with `curl`
- ☐ Started and stopped a service with `systemctl`
- ☐ Wrote and ran a shell script

Every core skill from Weeks 1–5, applied to a real, working project.

---

## Stretch Goals (Optional)

1. Add a `styles.css` file, link it from the HTML, redeploy.
2. Download an image with `wget`, place it in `content/`, embed it in the HTML.
3. Change the port — edit `/etc/apache2/ports.conf` to run on port 8080.
4. Find your IP with `ip addr` and view the site from a phone on the same WiFi.
5. Enable auto-start on boot: `sudo systemctl enable apache2`.
6. Schedule the deploy with cron every hour (bridges into Week 6).

---

## Teaching Notes

- **Don't skip Step 5** — watching vi fail to write and fixing it with `chown` is the moment permissions become real.
- **Do Step 8 with two terminals side by side.** The live log update while clicking in the browser is genuinely magical.
- If something fails, resist the urge to fix it for them. Ask: *"What command tells us what's happening?"* The answer is almost always `tail -f` on a log.
- Step 10 (the deploy script) is where they graduate from "someone who ran commands" to "someone who built a deployment pipeline." Celebrate it.
