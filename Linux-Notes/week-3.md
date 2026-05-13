# Week 3 — Viewing and Editing Text

The goal this week is helping them realize **almost everything in Linux is just text** — configs, logs, scripts, settings. Once they're comfortable reading and editing text files from the terminal, the system stops feeling like a black box.

---

## Commands to Cover

### Viewing Files
- `cat file.txt` — print the whole file to the screen
- `cat file1.txt file2.txt` — print multiple files in order
- `less file.txt` — scroll through a file (press `q` to quit, `/` to search)
- `more file.txt` — older, simpler version of `less`
- `head file.txt` — first 10 lines
- `head -n 20 file.txt` — first 20 lines
- `tail file.txt` — last 10 lines
- `tail -n 20 file.txt` — last 20 lines
- `tail -f file.log` — follow a file live as it grows (great for logs)
- `wc file.txt` — count lines, words, and characters
- `wc -l file.txt` — count just lines

### Searching Inside Files
- `grep "word" file.txt` — find lines containing "word"
- `grep -i "word" file.txt` — case-insensitive search
- `grep -n "word" file.txt` — show line numbers
- `grep -r "word" foldername` — search recursively in a folder
- `grep -v "word" file.txt` — show lines that do **not** contain "word"

### Basic Redirection (preview for Week 5)
- `echo "hello" > file.txt` — write text into a file (overwrite)
- `echo "more" >> file.txt` — append text to a file
- `cat file.txt > copy.txt` — copy content via redirection

### Editing with Nano
- `nano file.txt` — open or create a file in nano
- `Ctrl + O` — save (write Out)
- `Ctrl + X` — exit
- `Ctrl + K` — cut a line
- `Ctrl + U` — paste a line
- `Ctrl + W` — search inside the file
- `Ctrl + G` — help screen

### Comparing Files
- `diff file1.txt file2.txt` — show differences between two files
- `cmp file1.txt file2.txt` — quick check if two files differ

---

## Small Tasks

Give these one by one. Each takes 5–15 minutes.

### 1. The Diary
Create a file called `diary.txt` using `nano`. Write 5 sentences about their day. Save and exit. Then `cat` the file to confirm.

### 2. Peek and Skim
Download a long text file (e.g., a public-domain book from Project Gutenberg) or generate one with:

```
seq 1 1000 > numbers.txt
```

Now have them:
- View it with `cat` (and watch it fly by)
- View it with `less` and scroll around
- Show only the first 15 lines with `head -n 15`
- Show only the last 5 lines with `tail -n 5`

### 3. The Log Watcher
In one terminal, run:

```
tail -f /var/log/syslog
```

In another terminal, do something simple (e.g., `sudo apt update`). Watch the log update live. This teaches them that the system is always talking — they just have to listen.

### 4. Word Hunt
Using a text file of their choice (the diary works fine):
- Count total lines with `wc -l`
- Find all lines containing the word "today" using `grep`
- Find them case-insensitively with `grep -i`
- Show those lines **with line numbers** using `grep -n`

### 5. Build a Shopping List
Use `nano` to create `shopping.txt`. Add 10 items, one per line. Save. Then:
- Use `grep` to find lines containing "milk"
- Use `wc -l` to count how many items they have
- Use `tail -n 3` to see the last 3 items

### 6. Append Practice
- Use `echo "buy bread" >> shopping.txt` to add a new item
- Verify with `cat`
- Now overwrite the whole file with `echo "start over" > shopping.txt`
- Talk about the difference between `>` and `>>` — one **replaces**, one **adds**

### 7. The Config File Tour
Have them open (read-only is fine) a real config file:

```
less /etc/hosts
less /etc/os-release
```

Talk about what they see. The point isn't to understand every line — it's to realize **config files are just text files** they could edit if they wanted.

### 8. Find the Needle
Create a folder with 5 text files. Put the word "treasure" inside one of them, somewhere in the middle. Have them find which file contains it using:

```
grep -r "treasure" foldername
```

### 9. The Diff Drill
- Copy `shopping.txt` to `shopping-v2.txt`
- Open `shopping-v2.txt` in `nano` and change 2 lines
- Run `diff shopping.txt shopping-v2.txt`
- Read the output together — this is how programmers track changes

### 10. The Mini Journal
End the week with a small real-world project:
- Create a folder called `journal`
- Each day, create a new file like `2026-05-13.txt` using `nano`
- Write a few lines
- Use `ls` to see the growing collection
- Use `grep -r "happy" journal/` to find all entries where they wrote about being happy

---

## Teaching Notes

- **Don't introduce `vim`.** It's a rite of passage that can wait years. Nano is enough.
- The `Ctrl + X` and `Ctrl + O` keystrokes feel weird at first — let them practice on throwaway files until it's automatic.
- When viewing files with `less`, **remind them to press `q` to quit**. Many beginners get stuck and close the whole terminal.
- The biggest mindset shift this week: **text files are everywhere, and the terminal is how you talk to them.** Reinforce that idea often.
- If they ask "how do I copy text from the terminal" — it's usually `Ctrl + Shift + C` and `Ctrl + Shift + V` in most Linux terminals. Worth mentioning.
- Celebrate when they edit and save their first file. It's a real milestone.
