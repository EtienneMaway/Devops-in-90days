# Day 2 — Reading & searching files

## Concept: Finding information inside files, fast

Yesterday was about moving around and looking at *where* things are. Today is about looking
*inside* them. This is one of the most-used skills of a Software, DevOps, or Cloud engineer:
pulling the one line that matters out of a file that has thousands.

Real situations these commands solve:

- Your API crashes in production and you need to find the error.
- A log file has 10,000 lines but you only care about the last 50.
- You need every place a function is used across a large codebase.
- You want to know which `git` commands you ran yesterday.
- A server is running and you want to watch new logs appear live.

---

## Commands at a glance

| Command | Full name | What it does | Example |
|---|---|---|---|
| `cat` | Con**cat**enate | Print a file's contents | `cat package.json` |
| `less` | Less | Read a big file page by page | `less app.log` |
| `head` | Head | Show the **first** lines of a file | `head -20 app.log` |
| `tail` | Tail | Show the **last** lines of a file | `tail -50 app.log` |
| `tail -f` | Tail follow | Watch new lines appear in real time | `tail -f app.log` |
| `grep` | **G**lobal **r**egular **e**xpression **p**rint | Search for matching text | `grep "error" app.log` |
| `wc -l` | **W**ord **c**ount (lines) | Count lines | `wc -l app.log` |
| `\|` | Pipe | Send one command's output into the next | `cat app.log \| grep "error"` |

---

## Command details

### `cat` — Show me the whole file
`cat` stands for **concatenate**. It can join files together, but day to day you'll use it
to print a file's contents straight to the terminal.

```bash
cat package.json          # inspect a project's scripts without opening the editor
cat file1.txt file2.txt   # print both, one after the other
cat > notes.txt           # type text, then Ctrl + D to save and exit
```

Use `cat` for **small** files — a config file, a short script. For large files, use `less`.

### `less` — Read big files without drowning
A production log can be 200,000 lines. `cat`-ing it floods your terminal. `less` opens it
in a scrollable viewer instead.

```bash
less production.log
less /var/log/nginx/error.log
```

Inside `less`:

| Key | Action |
|---|---|
| `Space` | Forward one page |
| `b` | Back one page |
| `g` | Jump to the start |
| `G` | Jump to the end |
| `/error` | Search forward for "error" |
| `n` / `N` | Next / previous match |
| `q` | Quit |

### `head` / `tail` — Just the ends
Logs grow by appending, so the **newest** events are at the bottom — which is why `tail` is
used constantly.

```bash
head -10 app.log     # first 10 lines
head -5 users.csv    # peek at a CSV header before importing it
tail -20 app.log     # last 20 lines (most recent events)
```

### `tail -f` — Watch logs live
`-f` means **follow**. Instead of printing and exiting, it keeps the file open and streams
each new line as it's written.

```bash
tail -f app.log
tail -f ~/.pm2/logs/portfolio-error.log   # follow a PM2-managed Node app
```

Press `Ctrl + C` to stop. This is the go-to when an app works locally but fails after deploy.

### `grep` — Search inside files
`grep` finds lines that match a pattern. It's one of the most valuable commands you'll learn.

```bash
grep "error" app.log       # lines containing lowercase "error" only
grep -i "error" app.log    # -i = ignore case: matches error, Error, ERROR
```

**The combo worth memorizing — `grep -rin`:**

```bash
grep -rin "error" /var/log 2>/dev/null | head
```

| Part | Meaning |
|---|---|
| `-r` | **Recursive** — search subdirectories too |
| `-i` | **Ignore case** |
| `-n` | Show the **line number** |
| `"error"` | The text to find |
| `/var/log` | Where to search |
| `2>/dev/null` | Hide "permission denied" noise |
| `\| head` | Show only the first 10 results |

Output format is `filename:line_number:matching_text`:

```
/var/log/app.log:56:error: Request timeout
```

More grep you'll reach for:

```bash
# Find every use of a symbol across a project
grep -rin "JwtService" src/

# Skip dependencies and git metadata (cleaner + much faster)
grep -rin --exclude-dir=node_modules --exclude-dir=.git "JwtService" .

# Match several patterns at once (-E = extended regex, | means OR)
grep -Ein "error|failed|timeout" app.log

# Show 3 lines of context around each match
grep -in -C 3 "exception" app.log
```

### `wc -l` — Count lines
`wc` is **word count**; `-l` counts lines.

```bash
wc -l app.log                      # 1520 app.log
grep -ic "error" app.log           # count error lines directly (grep -c)
find src -type f -name "*.ts" | wc -l   # how many TS files in the project
```

> `grep -c` counts matching lines itself, so prefer `grep -ic "error" app.log`
> over `grep -i "error" app.log | wc -l` — same answer, one command.

### `|` — Pipes: combining commands
A pipe sends one command's output straight into the next, building a small assembly line.

```bash
grep -i "error" app.log | tail -5
```

Read it left to right: find the error lines → keep only the last 5. That's the five most
recent errors (assuming the log is chronological).

> You rarely need `cat file | grep ...` — `grep` reads the file directly:
> `grep -i "error" app.log`.

### `history | grep` — Search commands you already ran
Forgot a command from yesterday? Your shell remembers it.

```bash
history | grep git        # every git command you've run
history 1 | grep docker   # on macOS/zsh, start from event 1, then filter
```

You can also press `Ctrl + R` to search your history interactively as you type.

---

## Daily and real-world examples

#### 1. Investigate a failed deploy
```bash
less /var/log/nginx/error.log
# then inside less:  /error   <Enter>,  n to jump between matches
```

#### 2. Watch an app's logs during a live request
```bash
tail -f app.log
# in a second terminal, hit the API — watch the request appear instantly
```

#### 3. Find everywhere a function is used
```bash
grep -rin --exclude-dir=node_modules "JwtService" .
```

#### 4. Count how often errors are happening
```bash
grep -ic "error" app.log
# 85   -> 85 of your log lines mention an error
```

#### 5. Get the five most recent errors only
```bash
grep -i "error" app.log | tail -5
```

#### 6. Recover a command you ran yesterday
```bash
history 1 | grep "docker run"
```

---

## Hands-on exercise

Create a real log file and practice on it.

```bash
# Step 1 — make a practice log
mkdir -p ~/day-02
printf 'INFO Server started\nERROR Database failed\nINFO Request successful\nWARN Slow query\nERROR Redis timeout\n' > ~/day-02/app.log

# Step 2 — read it
cat ~/day-02/app.log

# Step 3 — search for errors (with line numbers)
grep -in 'error' ~/day-02/app.log

# Step 4 — count the errors
grep -ic 'error' ~/day-02/app.log

# Step 5 — watch it live
tail -f ~/day-02/app.log
```

For Step 5, open a **second** terminal and append a line:

```bash
echo "ERROR New API failure" >> ~/day-02/app.log
```

Watch the first terminal — the new line appears automatically. `>>` **appends** (unlike `>`,
which overwrites). Press `Ctrl + C` to stop following.

---

## Five grep commands worth keeping

| Task | Command |
|---|---|
| Search an entire directory | `grep -rin "error" ./logs` |
| Exclude dependencies | `grep -rin --exclude-dir=node_modules "TODO" .` |
| Find several error types | `grep -Ein "error\|failed\|timeout" app.log` |
| Show lines around a match | `grep -in -C 3 "exception" app.log` |
| Count matching lines | `grep -ic "error" app.log` |

---

## Quick knowledge check

<details>
<summary><strong>1.</strong> You want to monitor an application log as new requests arrive. Which command?</summary>

&nbsp;

`cat app.log` &nbsp;·&nbsp; **`tail -f app.log`** &nbsp;·&nbsp; `head app.log`

**Answer: `tail -f app.log`** — keeps the file open and prints newly appended lines live.
</details>

<details>
<summary><strong>2.</strong> You want to find <code>ERROR</code>, <code>Error</code>, and <code>error</code> in all files under <code>src/</code>. Which command?</summary>

&nbsp;

**`grep -rin 'error' src/`** &nbsp;·&nbsp; `grep -n 'error' src/` &nbsp;·&nbsp; `wc -l src/`

**Answer: `grep -rin 'error' src/`** — `-r` recurses, `-i` ignores case, `-n` shows line numbers.
</details>

<details>
<summary><strong>3.</strong> You need the number of lines containing <code>timeout</code> in <code>app.log</code>. Which is best?</summary>

&nbsp;

`tail -10 app.log` &nbsp;·&nbsp; `less app.log` &nbsp;·&nbsp; **`grep -ic 'timeout' app.log`**

**Answer: `grep -ic 'timeout' app.log`** — `-c` counts matching lines, `-i` makes it case-insensitive.
</details>

---

## Get your score

The check above is for self-testing. For a graded version that records your result and emails it
to you, take the Day 2 check — a few questions, about a minute:

**[Take the Day 2 check →]()**

<!-- Replace REPLACE_WITH_DAY_2_FORM_LINK with your Google Form (quiz mode) share link. -->

It also signs you up to get a ping when the next day is published.

---

## Key takeaway

The goal isn't to memorize every command — it's to **combine** a few of them to solve real
problems. For debugging a real application, `tail -f`, `grep -rin`, `less`, and pipes do most
of the work.