# Chapter 2 — Working with Files and Directories

> **Course:** Linux & Shell Basics · **Chapter:** 2 of 8
> Read on the website: [learn.bazardev.com/linux-shell/02-files-and-directories](https://learn.bazardev.com/linux-shell/02-files-and-directories)
>
> 🇧🇩 **Prefer Bangla?** [Read this chapter in Bangla →](https://learn.bazardev.com/linux-shell/02-files-and-directories?lang=bn)

Chapter 1 taught you how to *look around*. This chapter teaches you how to actually *create, read, move, and delete* things — the commands you'll type more than any others for the rest of your time on a server.

## 1. Creating files and folders

```bash
mkdir my-project
```
Makes a new folder. To create nested folders in one go (instead of one at a time):
```bash
mkdir -p my-project/src/components
```
`-p` creates every folder in the path that doesn't already exist yet, without complaining.

```bash
touch notes.txt
```
Creates an empty file (or, if the file already exists, just updates its "last modified" time without changing its contents).

## 2. Reading what's inside a file

```bash
cat notes.txt
```
Dumps the entire file straight to your screen. Great for short files, useless for anything long — it just floods your terminal.

```bash
less notes.txt
```
Opens the file one screen at a time — scroll with arrow keys or space, search with `/word`, quit with `q`. This is what you want for anything longer than a few lines.

```bash
head notes.txt           # first 10 lines
tail notes.txt           # last 10 lines
tail -f /var/log/app.log # keep watching a file as new lines get added, live
```

That last one, `tail -f`, is worth remembering on its own — it's exactly how you watch a log file in real time while something is happening, the same idea as `docker logs -f` from the Docker course.

## 3. Copying and moving

```bash
cp notes.txt notes-backup.txt
```
Copies a file — now two copies exist.

```bash
cp -r my-project my-project-backup
```
`-r` (recursive) is required to copy a **folder** — without it, `cp` refuses, because it won't guess whether you meant to copy everything inside.

```bash
mv notes.txt archive/notes.txt
```
Moves a file (or folder) somewhere else. `mv` is also how you **rename** something — moving a file to a new name in the same folder *is* renaming it:
```bash
mv notes.txt final-notes.txt
```

The core difference to remember: `cp` leaves the original in place and creates a second copy; `mv` doesn't — the file only exists in the new location afterward.

## 4. Deleting — and a serious warning

```bash
rm notes.txt
```
Deletes a file. Permanently — there's no recycle bin to recover it from.

```bash
rm -r my-project
```
`-r` deletes a folder and everything inside it, recursively.

```bash
rm -rf my-project
```
`-f` ("force") skips confirmation prompts and ignores errors on top of `-r`.

**Read this twice.** `rm -rf` is one of the most quoted "I destroyed my own server" stories in every developer's career, almost always for the same reason: being in the wrong folder when you run it, or a typo turning a specific path into something much broader. Before running `rm -rf anything`:
- Run `pwd` first, so you're certain which folder you're actually in.
- Run `ls` on the exact path first, to see what you're actually about to delete.
- Never run `rm -rf` with a path that starts with just `/` or has a stray space in it (`rm -rf / home/user/old-folder` — notice the space after `/` — deletes something very different from `rm -rf /home/user/old-folder`).

This is exactly why professional workflows (including this one) treat destructive commands as something to slow down for, not something to run on autopilot.

## 5. Wildcards: doing something to many files at once

```bash
ls *.log          # every file ending in .log
rm *.tmp           # delete every file ending in .tmp
cp *.php backup/   # copy every .php file into backup/
```

`*` means "anything, any length, including nothing." `?` means "exactly one character":
```bash
ls file?.txt      # matches file1.txt, fileA.txt — but not file10.txt
```

## 6. Finding files when you don't know where they are

```bash
find . -name "*.log"
```
Searches starting from the current folder (`.`) downward, through every subfolder, for anything matching the pattern. This is slower than other tools but searches live, right now, exactly where you point it — the most reliable option when you're not sure where something is.

```bash
which php
```
Tells you exactly which installed program would run if you typed `php` — useful when you have multiple versions installed and aren't sure which one is active.

## 7. Key takeaways

- `mkdir -p` creates nested folders in one command; `touch` creates an empty file.
- `cat` dumps a whole file at once; `less` lets you scroll through it; `tail -f` watches a file live, exactly like `docker logs -f`.
- `cp` keeps the original and makes a copy; `mv` moves (and is also how you rename things).
- `rm -rf` is permanent and recursive — always confirm your current folder and the exact path before running it.
- `*` and `?` are wildcards for matching multiple files at once; `find` searches the filesystem live for files matching a pattern.

## 8. Test yourself (exam prep)

<details>
<summary><strong>Q1. What's the difference between `cp` and `mv`?</strong></summary>

`cp` creates a second copy of a file or folder while leaving the original in place — afterward, two copies exist. `mv` relocates the file — afterward, it only exists in the new location. Renaming a file is really just `mv`-ing it to a new name in the same folder.
</details>

<details>
<summary><strong>Q2. Why does `cp` refuse to copy a folder unless you add `-r`?</strong></summary>

Copying a folder means copying everything inside it too, which could be a lot of files. `cp` requires the explicit `-r` (recursive) flag so you can't accidentally trigger a large copy operation by mistake.
</details>

<details>
<summary><strong>Q3. Before running `rm -rf some-folder`, what two things should you check first?</strong></summary>

Run `pwd` to confirm exactly which folder you're currently in, and `ls` on the exact path you're about to delete, to see precisely what will be removed. `rm -rf` is permanent and has no undo, so confirming both before running it prevents the most common "accidentally deleted the wrong thing" mistakes.
</details>

<details>
<summary><strong>Q4. What does `tail -f /var/log/app.log` do, and where have you seen this idea before?</strong></summary>

It keeps the file open and prints new lines as they're added, live, instead of showing a fixed snapshot. It's the same idea as `docker logs -f` from the Docker course — watching output happen in real time instead of checking it after the fact.
</details>

## 9. Free resources

- Official reference: [GNU Coreutils manual](https://www.gnu.org/software/coreutils/manual/coreutils.html) (covers `cp`, `mv`, `rm`, `cat`, and most commands in this chapter)
- YouTube: **"The 50 Most Popular Linux & Terminal Commands - Full Course for Beginners"** — channel: *freeCodeCamp.org*

---

**Next:** Chapter 3 — Permissions and Ownership *(coming soon)*
