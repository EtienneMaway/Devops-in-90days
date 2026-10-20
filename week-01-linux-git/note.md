# Day 1 — Filesystem navigation & inspection

## Concept: How to move and look around

Before you can fix, deploy, or clean anything on a computer or server, you need to know
**where you are**, **what's around you**, and **how much space things take**.
These commands are your eyes and legs in the terminal.

---

## Commands at a glance

| Command | Full name | What it does |
|---|---|---|
| `pwd` | **P**rint **W**orking **D**irectory | Shows the full path of the folder you are in right now |
| `cd` | **C**hange **D**irectory | Moves you into another folder |
| `ls` | **L**i**s**t | Shows what's inside a folder |
| `du` | **D**isk **U**sage | Shows how much space files and folders are using |
| `sort` | Sort | Puts lines in order (smallest → biggest by default) |
| `df` | **D**isk **F**ree | Shows how full each disk is (used vs. free space) |
| `cat` | Con**cat**enate | Prints a file's contents (originally: joins files together) |
| `echo` | Echo | Prints text back to you, or writes it into a file |
| `find` | Find | Searches for files and folders, starting from the folder you give it |

---

## Command details

### `pwd` — Where am I?
```bash
pwd
# /Users/etienne/projects
```
Use it whenever you feel lost, especially before running something risky like deleting files.

### `cd` — Move around
```bash
cd projects      # go into the "projects" folder
cd ..            # go up one folder
cd ~             # go to your home folder (same as just "cd")
cd /             # go to the root, the very top of the system
cd -             # jump back to the folder you were in before
```

### `ls -lah` — What's in here?
- `-l` → **long** view (permissions, owner, size, date)
- `-a` → show **all** files, including hidden ones like `.env` and `.git`
- `-h` → **human** sizes (`4K`, `12M`, `1.2G` instead of raw bytes)

```bash
ls -lah
# drwxr-xr-x   5 etienne  staff   160B Oct 19 19:00 src
# -rw-r--r--   1 etienne  staff   1.2K Oct 19 18:40 package.json
# -rw-r--r--   1 etienne  staff    85B Oct 19 18:41 .env
```

### `du -h` — How big is it?
- `-h` → human-readable sizes
- `-s` → **summary**: one total per item, not every subfolder
- `-d 1` → only go **1 level deep**

```bash
du -sh node_modules
# 312M    node_modules
```

### `sort -h` — Put it in order
Sorts lines from smallest to biggest. The `-h` flag makes it understand sizes,
so `2G` correctly comes after `500M`.

### `df -h` — Is my disk full?
```bash
df -h
# Filesystem   Size   Used  Avail  Use%  Mounted on
# /dev/sda1     40G    31G     9G   78%  /
```
On a server, this is the first thing to check when an app suddenly crashes or can't write files.

### `cat` — Show me the file
```bash
cat .env
cat part1.txt part2.txt > full.txt   # join two files into one
```
`cat` is best for short files. For long files like logs, use `less filename`
(scroll with the arrow keys, search with `/word`, quit with `q`).

### `find` — Search for files
```bash
find . -name '*.yaml'
```
- `.` → start searching **here** (the current folder) and go down into all subfolders
- `-name '*.yaml'` → match names ending in `.yaml`

Note: `find` searches from the folder you give it, not the whole system.
Use `find / ...` to search everything (slow, and needs permissions).

---

## Daily and real-world examples

#### 1. Find all `.yaml` files in a project
You want to locate config files, e.g. Docker Compose or GitHub Actions files.
```bash
find . -name '*.yaml'
# ./docker-compose.yaml
# ./.github/workflows/deploy.yaml
```

#### 2. Find the 3 biggest folders eating your disk space
Your laptop or server is running out of space and you want to know who's guilty.
```bash
du -h -d 1 2>/dev/null | sort -h | tail -4
```
- `du -h -d 1` → size of each folder, one level deep
- `2>/dev/null` → hide "permission denied" errors
- `sort -h` → order from smallest to biggest
- `tail -4` → show the last 4 lines: the 3 biggest folders + the total (`.`)

#### 3. Create a file, write text into it, and read it back
```bash
echo 'Hello World' > file.txt                         # > creates/overwrites the file
echo 'More text added in the same file' >> file.txt   # >> adds to the end
cat file.txt
# Hello World
# More text added in the same file
```
⚠️ Be careful: `>` **replaces** everything in the file. Use `>>` to add.

#### 4. Server app crashed: is the disk full?
Your NestJS app on the VPS stopped writing logs or uploads.
```bash
df -h                          # check if the disk is at 100%
cd /var/log                    # go to where system logs live
du -sh * 2>/dev/null | sort -h # see which logs are huge
```

#### 5. Find every `node_modules` folder hiding on your machine
These folders get huge. Find them before deciding what to clean.
```bash
cd ~/projects
find . -type d -name node_modules -prune
du -sh */node_modules 2>/dev/null | sort -h
```
- `-type d` → only directories
- `-prune` → don't search inside each one it finds (much faster)

#### 6. Check a project's environment file before deploying
```bash
cd ~/projects/my-api
ls -lah          # is .env actually there? (it's hidden, so you need -a)
cat .env         # are the values correct?
```

#### 7. Find big files over 100MB
Useful when cleaning old videos, database dumps, or Docker images.
```bash
find ~ -type f -size +100M 2>/dev/null
```

#### 8. Jump between two folders quickly
Working in an API folder and a frontend folder at the same time:
```bash
cd ~/projects/api
cd ~/projects/web
cd -      # back to api
cd -      # back to web
```

---

## Quick symbols cheat sheet

| Symbol | Meaning |
|---|---|
| `/` | Root, the very top of the filesystem |
| `~` | Your home folder (`/Users/etienne` on Mac, `/home/etienne` on Linux) |
| `.` | The current folder |
| `..` | The parent folder (one level up) |
| `*` | Wildcard: "anything" (e.g. `*.json`) |
| `\|` | Pipe: send one command's output into the next command |
| `>` | Write output to a file (overwrite) |
| `>>` | Append output to a file |