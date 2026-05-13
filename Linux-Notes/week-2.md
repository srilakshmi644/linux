Week 2 — Navigation and Files
The goal this week is building muscle memory for moving around the filesystem and handling files. Mix commands with small tasks so it feels like exploration, not drills.

Commands to Cover
Navigation

pwd — where am I right now?
ls — what's in this folder?
ls -l — detailed list (permissions, size, date)
ls -a — show hidden files (ones starting with .)
ls -lh — human-readable sizes
cd foldername — enter a folder
cd .. — go up one level
cd ~ — go to home
cd - — go to the previous folder
cd / — go to the root of the filesystem
tree — visual tree of folders (may need sudo apt install tree)

Creating

mkdir myfolder — make a folder
mkdir -p a/b/c — make nested folders in one go
touch file.txt — create an empty file

Copying, Moving, Renaming

cp file.txt copy.txt — copy a file
cp -r folder1 folder2 — copy a folder and everything in it
mv file.txt newname.txt — rename a file
mv file.txt ../ — move a file up one folder

Deleting (teach carefully!)

rm file.txt — delete a file
rm -r foldername — delete a folder and its contents
rmdir emptyfolder — delete an empty folder (safer)
Warn clearly: there is no recycle bin in the terminal.

Wildcards (tiny intro)

ls *.txt — everything ending in .txt
rm temp* — everything starting with temp


Small Tasks
Give these one by one, not all at once. Each takes 2–10 minutes.
1. The Home Tour
Run pwd, then ls -la in your home folder. Pick three hidden files and guess what they do.
2. Build a Filing Cabinet
Create this structure in one mkdir -p command:

practice/docs/letters
practice/docs/bills
practice/photos/2025

Verify with tree practice.
3. Populate the Cabinet
cd into each folder and use touch to create believable filenames:

electricity.txt in bills
mom.txt in letters
beach.jpg in photos/2025

Then go back to practice and list everything.
4. Cleanup Drill
Make 5 junk files named temp1.txt through temp5.txt. Delete them all with one wildcard command.
5. The Rename Challenge

Create a file called draft.txt
Rename it to final.txt
Copy it to final-backup.txt
List the folder and confirm all three states happened correctly

6. Lost and Found
cd into a deep folder (like practice/docs/letters), then get back home using three different methods:

cd ~
cd
cd ../../..

7. The Hidden File Mystery
Create a file called .secret.txt. Run ls — where did it go? Run ls -a — there it is. Explain the dotfile convention (hidden by default, usually config).
8. Move Day
Put a file in the wrong folder on purpose (e.g., a .jpg in bills). Move it to the right place with mv.
9. The Nuclear Option (with a Net)
Create a folder called danger with a few files inside. Delete it with rm -r danger. Talk about why this command deserves respect — no undo.
10. Mini Scavenger Hunt
Pre-create a folder with 20 random files (some .txt, some .jpg, some .log). Ask them to:

List only the .log files
Delete only those, without touching the others


Teaching Notes

Let them make mistakes inside the practice folder — that's what it's for.
After each task, ask them to explain what just happened in their own words.
Encourage using Tab to autocomplete filenames — it prevents typos and builds speed.
Show them the up arrow to recall previous commands. Small quality-of-life wins keep morale high.
By the end of the week, they should be able to organize a messy folder without thinking about the commands.
