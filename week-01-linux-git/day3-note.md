# Day 3 — Files, permissions & processes

## Concept: Managing files, who can touch them, and what's running

Yesterday was about reading and searching files. Today is about three layers that sit
underneath every deploy you'll ever do:

1. **Files** — copying, moving, renaming, and removing them.
2. **Permissions** — who's allowed to read, change, or run a file.
3. **Processes** — how programs run, how to find them, and how to stop them.

Real situations these commands solve:

- Your deploy script returns `Permission denied` and won't run.
- A config file belongs to `root` but your deploy user needs to edit it.
- A test server is still running in the background and you need to stop it.
- Your API went slow and something is eating CPU — you need to find what.
- You want a backup of a config file before you change it on a live server.

---

## Commands at a glance

| Command | What it does | Example |
|---|---|---|
| `cp` | Copy a file or directory | `cp nginx.conf nginx.conf.backup` |
| `mv` | Move or rename a file | `mv app.js server.js` |
| `rm -i` | Remove a file, asking first | `rm -i app-backup.js` |
| `chmod +x` | Make a file executable | `chmod +x deploy.sh` |
| `chmod 644` / `755` | Set permission bits by number | `chmod 755 deploy.sh` |
| `chown` | Change a file's owner/group | `sudo chown etienne:staff app.js` |
| `umask` | Show/set default permissions for new files | `umask` |
| `ps aux` | List running processes (snapshot) | `ps aux \| grep '[n]ode'` |
| `top` / `htop` | Watch processes and resources live | `top` |
| `kill` | Send a signal to a process (by PID) | `kill 4821` |

---

## 1. File management: `cp`, `mv`, `rm -i`

### `cp` — Copy files
```bash
cp app.js app-backup.js        # copy a single file
cp -R project project-backup   # copy a whole directory (-R = recursive)
```

Real-world use: before changing a config on a server, make a backup first.

```bash
cp nginx.conf nginx.conf.backup
```

This is fine for small, quick backups. Production backups usually need more robust tooling.

### `mv` — Move or rename
```bash
mv app.js server.js     # rename in place
mv server.js ~/day-03/  # move into another directory
```

`mv` moves or renames — it doesn't leave a second copy behind.

### `rm -i` — Remove, with a confirmation
```bash
rm app-backup.js     # removes immediately, no prompt
rm -i app-backup.js  # asks: remove app-backup.js?  before deleting
```

**Important:** `rm` does **not** move files to the Trash. Be especially careful with
`rm -r` and `rm -rf`, which delete whole directory trees. Never run a destructive command
on an unfamiliar path without checking it first.

---

## 2. Permissions: why `chmod` matters

Say you write a shell script to deploy your Node app:

```bash
./deploy.sh
# bash: ./deploy.sh: Permission denied
```

The file exists, but it doesn't have permission to run. That's what `chmod` ("change mode")
fixes — it changes a file's permission bits.

### `chmod +x` — Make a file executable
```bash
chmod +x deploy.sh
./deploy.sh
```

A shell script also needs a valid interpreter line (a **shebang**) on the first line:

```bash
#!/bin/bash      # or, on macOS:  #!/bin/zsh
```

The shebang tells the OS which interpreter should run the script.

You'll use `chmod +x` on nearly every script this week — deploy scripts, backup scripts,
project setup scripts, anything you run in a CI/CD pipeline.

> Making a script executable doesn't make it safe or guarantee it works. Its contents,
> interpreter, and environment still matter.

---

## 3. `chmod 644` vs `chmod 755` — what the numbers mean

This is the core idea of Day 3.

Every file has three permission **categories**:

| Who | Symbol | Meaning |
|---|---|---|
| Owner | `u` | the user who owns the file |
| Group | `g` | users in the file's group |
| Others | `o` | everyone else |

And three permission **types**, each with a number:

| Permission | Symbol | Value | Meaning |
|---|---|---|---|
| Read | `r` | **4** | view the contents |
| Write | `w` | **2** | change the contents |
| Execute | `x` | **1** | run it (for a directory: traverse into it) |

Add the values to get each category's digit.

### Reading `644`
```bash
chmod 644 notes.txt
```

| Category | Number | Permissions |
|---|---|---|
| Owner | `6` | `rw-` — read + write (4 + 2) |
| Group | `4` | `r--` — read only |
| Others | `4` | `r--` — read only |

Full string: `-rw-r--r--` (the leading `-` means "regular file").

Use for: text files, config files, source files — readable and editable, no need to execute.

### Reading `755`
```bash
chmod 755 deploy.sh
```

| Category | Number | Permissions |
|---|---|---|
| Owner | `7` | `rwx` — read + write + execute (4 + 2 + 1) |
| Group | `5` | `r-x` — read + execute (4 + 1) |
| Others | `5` | `r-x` — read + execute |

Full string: `-rwxr-xr-x`.

Use for: executable scripts, and directories people need to enter.

### The difference to remember

| Mode | Owner | Group | Others | Typical use |
|---|---|---|---|---|
| `644` | read, write | read | read | text & config files |
| `755` | read, write, execute | read, execute | read, execute | scripts; accessible dirs |

Two things that trip people up:

- `755` does **not** mean everyone can edit the file. Only the owner has write here.
- For a **directory**, execute (`x`) means "can traverse into it," not "can run it." Think
  about directory permissions separately from file permissions.

### Try it
```bash
touch notes.txt
chmod 644 notes.txt
ls -l notes.txt        # -rw-r--r-- ... notes.txt

chmod 755 notes.txt
ls -l notes.txt        # -rwxr-xr-x ... notes.txt
```

You changed the permission bits without touching the file's contents. Use `ls -l` any time
to inspect permissions.

---

## 4. Default permissions & `umask`

When you create a file or directory, the OS gives it default permissions. The **starting**
modes (before any filtering) are:

| Type | Starting mode | Meaning |
|---|---|---|
| Regular file | `666` (`rw-rw-rw-`) | read + write, no execute |
| Directory | `777` (`rwxrwxrwx`) | read + write + execute |

But the permissions you actually see are more restrictive, because of `umask`.

### What `umask` does
`umask` (user file-creation mode mask) **clears** selected permission bits from those
starting modes whenever something new is created. Think of it as a default security filter.

A common value is `022`:

| New thing | Starts at | After `umask 022` |
|---|---|---|
| File | `666` | `644` — owner read/write, everyone else read-only |
| Directory | `777` | `755` — owner full, everyone else read + traverse |

> It's often explained as subtraction, but `umask` technically *clears bits* — it's a
> bitmask, not arithmetic. The result lines up with subtraction in the common cases.

### Check it on your Mac
```bash
umask                                  # often prints 022

mkdir ~/permission-test
touch ~/permission-test/file.txt
ls -ld ~/permission-test               # drwxr-xr-x ... permission-test
ls -l  ~/permission-test/file.txt      # -rw-r--r-- ... file.txt
```

To see permissions both symbolically and numerically (macOS):

```bash
stat -f '%Sp %Lp %N' ~/permission-test/file.txt
# %Sp = symbolic, %Lp = numeric mode, %N = file name
```

Results can differ because of ACLs or filesystem behavior.

---

## 5. `chown` — change who owns a file

`chown` ("change owner") changes **who owns** a file. That's different from `chmod`:

- `chmod` changes **what people can do** with a file.
- `chown` changes **who owns** it.

### Example
```bash
ls -l app.js
# -rw-r--r--  1 root  wheel  1200 Oct 29 app.js   (owner: root, group: wheel)

sudo chown etienne app.js          # change owner only
sudo chown etienne:staff app.js    # change owner and group
```

On Linux the group is often `www-data`, `deploy`, or similar.

### Real-world use

**Deploying a web app** — files belong to `root`, but your deploy user needs to manage them:
```bash
sudo chown -R etienne:etienne /var/www/portfolio   # -R = recursive
```
Use this only when that ownership is actually correct — don't blindly re-own a whole
production tree.

**Fixing a permission error** — an app can't write to a directory owned by someone else.
Inspect first, then decide who genuinely needs access:
```bash
ls -ld /var/www/portfolio
```

**Database & backup files** — a service (like `_mysql`) needs its data files owned by its
own service account. Re-owning them to a normal user can stop the database from starting.

### `chmod` vs `chown` vs `umask`

| Command | Controls | Example |
|---|---|---|
| `chmod` | permissions | `chmod 644 notes.txt` |
| `chown` | owner & group | `chown etienne:staff notes.txt` |
| `umask` | default permissions for new files | `umask 022` |

`chown` usually needs `sudo` when you're handing a file to another user. The principle:
**`umask` sets permissions at creation, `chmod` changes them afterward, `chown` changes
ownership.**

---

## 6. Processes: what happens when a program runs

A **process** is a running instance of a program. When you start a server:

```bash
npm run start
```

the OS creates one or more processes to run it. Each has a **PID** (Process ID) — a number
the OS uses to identify it. Node servers, MySQL, Redis, your browser, a background job queue
— all processes. Knowing how to find them is how you track down what's eating CPU or memory.

### `ps aux` — list running processes (a snapshot)
```bash
ps aux
```

| Column | Meaning |
|---|---|
| `USER` | who's running it |
| `PID` | process ID |
| `%CPU` | CPU usage |
| `%MEM` | memory usage |
| `COMMAND` | what started it |

Find your Node server:
```bash
ps aux | grep node
# etienne  480  3.2  4.0 node dist/main.js
# etienne  512  0.0  0.1 grep node          <- the search itself
```

To hide the `grep` line, bracket the first letter so the pattern doesn't match itself:
```bash
ps aux | grep '[n]ode'
```

Or match more precisely:
```bash
pgrep -af node
```

> Several Node processes can run at once. Confirm the right PID *and* command before you
> stop anything.

### `top` / `htop` — watch resources live
`ps aux` is a snapshot; `top` refreshes continuously so you can watch CPU and memory in
real time — the first thing to run when an API goes slow.

```bash
top        # press q to quit
```

`htop` is a friendlier, interactive version. Install on macOS with Homebrew:
```bash
brew install htop
htop
```

`top` is usually available by default on a server, so it's the one worth knowing first.

### `kill` — stop a process
`kill` sends a **signal** to a process by its PID.

```bash
kill 4821       # SIGTERM (15): ask it to stop gracefully
kill -9 4821    # SIGKILL (9): force it to stop
```

| Command | Signal | Behavior |
|---|---|---|
| `kill PID` | SIGTERM | asks it to terminate gracefully (it can clean up first) |
| `kill -9 PID` | SIGKILL | forces termination — can't be caught or ignored |
| `kill -STOP PID` | SIGSTOP | suspends the process |
| `kill -CONT PID` | SIGCONT | resumes a suspended process |

**Best practice:** try `kill PID` first. Reach for `kill -9` only when a process won't stop
normally *and* you've confirmed it's the right one. Force-killing a database mid-write can
require recovery on restart. `kill` doesn't always mean "terminate" — it sends whatever
signal you name.

---

## Hands-on exercise: create, run, find, and stop a process

### Step 1 — Create a practice script
```bash
mkdir -p ~/day-03
cd ~/day-03
nano worker.sh
```

Inside nano, type:
```sh
#!/bin/sh

echo "Worker started. PID: $$"

while true; do
    sleep 2
    echo "Worker is still running..."
done
```

Save with `Ctrl + O`, `Enter`, then exit with `Ctrl + X`. The `$$` variable holds the
shell's own PID.

### Step 2 — Try to run it before it's executable
```bash
./worker.sh
# may print: Permission denied
```
That's the problem `chmod +x` solves.

### Step 3 — Make it executable and run
```bash
chmod +x worker.sh
./worker.sh
# Worker started. PID: 4821
# Worker is still running...
```
(Your PID will differ.)

### Step 4 — Find its process
In a **second** terminal:
```bash
ps aux | grep '[w]orker.sh'
```

### Step 5 — Stop it
```bash
kill 4821      # use the PID you found
```
The script stops. If it doesn't, check the process state before considering `kill -9`.

**The full sequence:** create → `chmod +x` → run → find the PID → `kill`.

---

## Push `day-03/notes.md` to GitHub

Write the notes in your own words — the permission-number table especially. Then:

```bash
git add day-03/notes.md
git commit -m "Add day 3 Unix notes"
git push
```

Run these from the root of your learning repo, with your remote already configured.

---

## Quick knowledge check

<details>
<summary><strong>1.</strong> What does <code>chmod 755</code> allow that <code>chmod 644</code> does not?</summary>

&nbsp;

Everyone can modify the file &nbsp;·&nbsp; **The owner can execute it, and group/others get execute too** &nbsp;·&nbsp; The file becomes owned by root

**Answer:** `755` adds **execute** (owner `rwx`, group/others `r-x`). `644` has no execute bit. It does *not* give anyone but the owner write permission.
</details>

<details>
<summary><strong>2.</strong> Which command should you try first to stop a process normally?</summary>

&nbsp;

`kill -9 PID` &nbsp;·&nbsp; `rm PID` &nbsp;·&nbsp; **`kill PID`**

**Answer:** `kill PID` sends SIGTERM and lets the process clean up. Use `kill -9` only if it won't stop.
</details>

<details>
<summary><strong>3.</strong> What's the main difference between <code>ps aux</code> and <code>top</code>?</summary>

&nbsp;

**`ps aux` shows a snapshot; `top` refreshes continuously** &nbsp;·&nbsp; `ps aux` stops processes; `top` starts them &nbsp;·&nbsp; They always behave identically

**Answer:** `ps aux` is a one-time snapshot; `top` keeps updating so you can watch resource use live.
</details>

---

## Get your score

The check above is for self-testing. For a graded version that records your result and emails
it to you, take the Day 3 check — a few questions, about a minute:

**[Take the Day 3 check →]()**

<!-- Replace with your Day 3 Google Form (quiz mode) share link. -->

It also signs you up to get a ping when the next day is published.

---

## Key takeaway

Day 3 is three layers of your machine: **files** (where code and data live),
**permissions** (who can touch or run them), and **processes** (what happens when the code
runs). Deploying a Node app to a Linux server needs all three — the deploy script must be
executable, the files owned and permissioned correctly, and the server process actually
running. Get comfortable here and debugging a deploy stops being a mystery.
