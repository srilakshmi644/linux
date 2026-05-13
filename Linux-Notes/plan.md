Teaching Linux to a Complete Beginner
A 5-week plan for teaching Linux to someone with no software background.
Before You Start
Explain why Linux exists before any commands. It's an operating system like Windows or macOS, but it powers most of the internet, Android phones, and supercomputers. The terminal isn't scary — it's just a text conversation with the computer.

Learning Plan
Week 1 — Orientation

Install a friendly distro (Ubuntu or Linux Mint), or use WSL on Windows.
Teach the desktop environment first to build comfort.
Open the terminal and explain the prompt (user@machine:~$).
Cover the core idea: everything in Linux is a file, and the filesystem is a tree starting at /.

Week 2 — Navigation and Files
Moving around, looking at things, creating and deleting. This is where muscle memory gets built.
Week 3 — Viewing and Editing Text
Reading files, basic editing with nano, understanding that config files are just plain text.
Week 4 — Permissions, Users, and Packages
Who can do what, the role of sudo, and installing software safely.
Week 5 — Processes, Pipes, and Putting It Together
Running programs, chaining commands with pipes, and small shell tricks that tie everything together.

Commands by Stage
Navigation

pwd — print working directory
ls — list files
ls -la — list all files with details
cd — change directory
cd .. — go up one level
cd ~ — go to home directory
tree — show directory as a tree

Files and Folders

mkdir — make a directory
touch — create an empty file
cp — copy
mv — move or rename
rm — remove (teach carefully)
rm -r — remove a directory and its contents

Viewing Text

cat — print a file
less — scroll through a file
head — first lines of a file
tail — last lines of a file
tail -f — follow a file as it grows (great for logs)

Searching

find — find files by name, size, date, etc.
grep — search text inside files
which — locate a command's binary
locate — fast filename search (requires updatedb)

Editing

nano — simple, beginner-friendly editor
vim — introduce much later, if ever

Permissions and Users

whoami — show current user
id — show user and group IDs
chmod — change file permissions
chown — change file ownership
sudo — run as superuser
passwd — change password

Packages (Ubuntu/Debian)

apt update — refresh package lists
apt upgrade — install available updates
apt install <package> — install software
apt remove <package> — uninstall software

Processes

ps — list processes
top or htop — live process monitor
kill — stop a process
& — run a command in the background
jobs — list background jobs
fg — bring a job to the foreground

System Info

df -h — disk space (human-readable)
du -sh — size of a directory
free -h — memory usage
uname -a — kernel and system info
uptime — how long the system has been running

Pipes and Redirection

| — send output of one command into another
> — redirect output to a file (overwrite)
>> — redirect output to a file (append)
< — feed a file as input
Example: ls | grep txt

Getting Help

man <command> — full manual
<command> --help — quick usage
tldr <command> — beginner-friendly examples (install with apt install tldr)


Teaching Tips

Give them a safe sandbox folder to experiment in so rm mistakes don't hurt.
Have them narrate what each command does out loud — it cements the mental model.
Assign tiny real projects:

Organize a folder of files
Find all photos larger than 1MB
Write a script that backs up a directory


Real tasks beat abstract drills. Always tie a command to something they actually want to do.
Don't rush vim. nano is enough for months.
Celebrate small wins — the terminal feels intimidating, and early confidence matters.
