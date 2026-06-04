# Week 3 — Viewing and Editing Text

The goal this week is helping them realize **almost everything in Linux is just text** — configs, logs, scripts, settings. Once they're comfortable reading and editing text files from the terminal, the system stops feeling like a black box.

---

## Setup — Create Sample Files First

Before any commands or tasks, have the learner create a `week3` practice folder with files that already contain known content. Copy-paste these into the terminal:

```
mkdir -p ~/week3 && cd ~/week3

# A numbers file with 1000 lines
seq 1 1000 > numbers.txt

# A shopping list with 10 items, some containing "milk"
cat > shopping.txt << 'EOF'
milk
bread
eggs
butter
chocolate milk
apples
rice
coffee
sugar
oat milk
EOF

# A diary file with the word "today" in some lines
cat > diary.txt << 'EOF'
today I went for a walk in the park
the weather was warm and sunny
today I cooked pasta for dinner
I watched a movie in the evening
today felt productive
I read a chapter of my book
tomorrow I want to wake up early
EOF

# A folder with 5 files, one containing "treasure"
mkdir -p hunt
echo "the sky is blue" > hunt/file1.txt
echo "I like apples" > hunt/file2.txt
echo "buried treasure lies here" > hunt/file3.txt
echo "the cat sat on the mat" > hunt/file4.txt
echo "hello world" > hunt/file5.txt
```

Now everything below will work as expected. All commands assume you're in `~/week3`.

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

### Editing with vi

`vi` has two main modes — **Normal mode** (for commands and navigation) and **Insert mode** (for typing text). You start in Normal mode.

**Opening and exiting**
- `vi file.txt` — open or create a file
- `i` — enter **Insert mode** (now you can type)
- `Esc` — leave Insert mode, back to Normal mode
- `:w` — save (write)
- `:q` — quit
- `:wq` — save and quit
- `:q!` — quit **without** saving (discard changes)

**Moving around (Normal mode)**
- `h` `j` `k` `l` — left, down, up, right (arrow keys also work)
- `0` — jump to start of line
- `$` — jump to end of line
- `gg` — go to top of file
- `G` — go to bottom of file

**Editing (Normal mode)**
- `x` — delete the character under the cursor
- `dd` — delete the whole line
- `yy` — copy ("yank") the whole line
- `p` — paste below the cursor
- `u` — undo
- `Ctrl + r` — redo

**Searching**
- `/word` — search forward for "word", press `Enter`
- `n` — jump to next match
- `N` — jump to previous match

### Comparing Files
- `diff file1.txt file2.txt` — show differences between two files
- `cmp file1.txt file2.txt` — quick check if two files differ

---

## Small Tasks

Give these one by one. Each takes 5–15 minutes.

### 1. The Diary Tour
- `cat diary.txt` to see the whole file
- `head -n 3 diary.txt` to see the first 3 lines
- `tail -n 2 diary.txt` to see the last 2 lines
- `wc -l diary.txt` to count how many lines it has

### 2. Peek and Skim
Use the `numbers.txt` file (1000 lines):
- View it with `cat numbers.txt` (watch it fly by)
- View it with `less numbers.txt` and scroll around (press `q` to quit)
- Show only the first 15 lines with `head -n 15 numbers.txt`
- Show only the last 5 lines with `tail -n 5 numbers.txt`

### 3. The Log Watcher
In one terminal, run:

```
tail -f /var/log/syslog
```

In another terminal, do something simple (e.g., `sudo apt update`). Watch the log update live. This teaches them that the system is always talking — they just have to listen.

> Note: on some systems the log file is `/var/log/messages` instead. If `syslog` doesn't exist, try that.

### 4. Word Hunt in the Diary
- Find all lines containing "today" with `grep "today" diary.txt`
- Try the same case-insensitively with `grep -i "TODAY" diary.txt`
- Show matching lines with line numbers using `grep -n "today" diary.txt`
- Show lines that do **not** contain "today" with `grep -v "today" diary.txt`

### 5. Shopping List Search
Using the pre-made `shopping.txt`:
- Find all items containing "milk" with `grep "milk" shopping.txt`
- Count how many items total with `wc -l shopping.txt`
- Show the last 3 items with `tail -n 3 shopping.txt`

### 6. Append vs Overwrite
- Add an item with `echo "buy bread" >> shopping.txt`
- Verify with `cat shopping.txt` — the new line should be at the end
- Now overwrite the whole file: `echo "start over" > shopping.txt`
- Run `cat shopping.txt` again — everything else is gone
- Talk about the difference: `>` **replaces**, `>>` **adds**

> Tip: regenerate the original shopping list by re-running the setup block if you want to keep practicing.

### 7. Find the Needle
Use the pre-made `hunt` folder. Find which file contains "treasure":

```
grep -r "treasure" hunt/
```

The output will show both the filename and the matching line. Explain how this works on huge codebases too — `grep -r` is one of the most-used commands in real life.

### 8. The Diff Drill
- Copy the shopping list: `cp shopping.txt shopping-v2.txt`
  - (First regenerate `shopping.txt` from the setup block if you overwrote it earlier)
- Open `shopping-v2.txt` in `nano` and change 2 lines
- Run `diff shopping.txt shopping-v2.txt`
- Read the output together — this is how programmers track changes

---

## Teaching Notes

- **Don't introduce `vim`.** It's a rite of passage that can wait years. Nano is enough.
- The `Ctrl + X` and `Ctrl + O` keystrokes feel weird at first — let them practice on throwaway files until it's automatic.
- When viewing files with `less`, **remind them to press `q` to quit**. Many beginners get stuck and close the whole terminal.
- The biggest mindset shift this week: **text files are everywhere, and the terminal is how you talk to them.** Reinforce that idea often.
- If they ask "how do I copy text from the terminal" — it's usually `Ctrl + Shift + C` and `Ctrl + Shift + V` in most Linux terminals. Worth mentioning.
- Celebrate when they edit and save their first file. It's a real milestone.
