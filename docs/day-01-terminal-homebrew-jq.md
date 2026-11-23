# Day 1: Terminal, Permissions, Homebrew and jq

> Part of my 12-month journey to becoming a junior Cloud Engineer (AWS, Terraform, automation).
> Written so a complete beginner can follow along and copy what I did.

**Time needed:** about 1.5–2 hours
**Cost:** £0 (nothing runs in the cloud today)
**Works on:** macOS (Apple Silicon, M1/M2/M3/M4). Most of it also works on Intel Macs.

---

## What you'll learn

| Thing | In one line |
| --- | --- |
| Terminal | Where engineers type commands to control the computer |
| Permissions | Who can read, change or enter a file or folder |
| Homebrew | A tool that installs other tools |
| JSON | A way of writing data that computers can read |
| jq | A tool that picks out the bit you want from JSON |

## Why this matters for cloud engineering

Cloud engineers spend most of their day in a terminal. Tools like Terraform, the AWS CLI, Git and SSH all run there. AWS replies to almost every request in **JSON**, so being able to read it, and pull data out of it with **jq**, is the first step towards automation.

---

## Step 1: Open the terminal and look around

Open **Terminal**: press `Cmd + Space`, type `Terminal`, then press Enter.

Run these one at a time:

```bash
echo $SHELL
pwd
uname -m
sw_vers
ls -la ~
git --version
```

| Command | What it does | What you should see |
| --- | --- | --- |
| `echo $SHELL` | Shows which shell (command reader) you're using | `/bin/zsh` |
| `pwd` | Shows which folder you're in ("print working directory") | `/Users/<your-name>` |
| `uname -m` | Shows your chip type | `arm64` = Apple Silicon |
| `sw_vers` | Shows your macOS version | A version number |
| `ls -la ~` | Lists everything in your home folder, including hidden files | A long list |
| `git --version` | Checks Git is installed | A version number |

> If `git --version` shows a pop-up asking to install **Command Line Tools**, click **Install**, wait, then run it again.

**Tip:** files and folders starting with a dot (like `.zshrc`) are **hidden**. They're usually settings for your tools.

---

## Step 2: Make a practice folder and file

```bash
mkdir -p ~/lab/day1 && cd ~/lab/day1
echo "hello" > test.txt
cat test.txt
ls -l test.txt
```

| Part | Meaning |
| --- | --- |
| `mkdir -p` | Make a folder (`-p` = also make any missing parent folders, and don't complain if it already exists) |
| `&&` | Only run the next command if the first one worked |
| `cd` | Move into a folder |
| `>` | Write the output into a file (replaces what's there) |
| `cat` | Show what's inside a file |

You'll see something like:

```
-rw-r--r--  1  your-name  staff  6  Nov 23 11:16  test.txt
```

The file is **6 bytes**: "hello" is 5 letters, plus 1 invisible "new line" character at the end.

---

## Step 3: Read permissions

The first part of each `ls -l` line shows **who can do what**.

### The rules

| Letter | Meaning |
| --- | --- |
| `d` (first character) | It's a **folder** |
| `-` (first character) | It's a **file** |
| `r` | Can **read** |
| `w` | Can **write** (change) |
| `x` | Can **enter** (folder) or **run** (file) |
| `-` (anywhere else) | **Not allowed** |

After the first character, the letters come in **3 groups of 3**:

```
d    rwx    r-x    r-x
│    YOU    GROUP  EVERYONE ELSE
└── folder or file?
```

### Examples

| Permissions | Plain English |
| --- | --- |
| `drwx------` | A **folder** only you can open, read and change. Nobody else can do anything. |
| `drwxr-xr-x` | A **folder**. You can do everything. Everyone else can read and enter, but **not change** it. |
| `-rw-r--r--` | A **file**. You can read and change it. Everyone else can only read it. |
| `-rw-------` | A **file** only you can read and change. ✅ Correct setting for secrets. |

**Tip:** always check the **first character** first (`d` or `-`), then read the three groups.

### 🔐 Security note

If you've used AWS before, you may have a `~/.aws` folder. It can contain secret keys.

- Check the permissions with `ls -l ~/.aws` (this shows names only, not contents).
- **Never** run `cat` on files in `~/.aws`, and **never** paste their contents online or into chats.
- Secret files should look like `-rw-------` (only you).

---

## Step 4: Homebrew, the tool that installs tools

**Homebrew** lets you install software with one command instead of hunting for installers on websites.

### Check if you already have it

```bash
brew --version
which brew
```

On Apple Silicon, `which brew` should show `/opt/homebrew/bin/brew`.

### If you don't have it

1. Go to **[brew.sh](https://brew.sh)** and copy the install command **from that page**.
2. Paste it into Terminal and run it.
3. **Read the end of the output.** It shows "Next steps": two commands to run. Run them.
4. Check it worked: `brew --version`

> ⚠️ **Why copy from the official site?** That command downloads and runs a script with full access to your Mac. Only ever take commands like this from the official source.

> ❓ **`zsh: command not found: brew`** after installing? You skipped the "Next steps" commands. They add Homebrew to your **PATH**, the list of folders your terminal checks when you type a command.

---

## Step 5: Challenge, use the built-in help

**Goal:** work out the commands yourself using Homebrew's own help. Try not to Google!

```bash
brew help
```

Use what it shows to:

1. **Search** for a package called `jq`
2. **Show info** about `jq` without installing it
3. **Install** `jq`

<details>
<summary>Stuck? Click for the answer</summary>

```bash
brew search jq
brew info jq
brew install jq
```

</details>

**What you'll notice while it installs:**
- **"Auto-updating Homebrew"**: it refreshes its list of software first.
- **"Would install 1 dependency: oniguruma"**: jq needs another small library to work, so Homebrew installs that too.
- **"bottle"**: a ready-made package built for your chip.

---

## Step 6: What is JSON, and what is jq?

**JSON** is a way of writing information so computers can read it, as labels and values:

```json
{"name": "Alex", "role": "cloud engineer"}
```

`name` is the label and `Alex` is the value.

**jq** picks out the bit you want:

```bash
echo '{"name":"Alex","role":"cloud engineer"}' | jq .role
```

Output:

```
"cloud engineer"
```

The `|` symbol is a **pipe**. It sends the output of the left command into the right command.

### Why cloud engineers need jq

Ask AWS "what servers do I have?" and it replies with a big block of JSON. jq lets your scripts pull out just the detail you need, such as server IDs, without anyone reading through it by hand. That's the start of automation.

---

## Step 7: Challenge, remove the quotes

In scripts, you usually want `cloud engineer`, not `"cloud engineer"`.

Run `jq --help` and find the option for **raw output**. Add it to the command above.

<details>
<summary>Stuck? Click for the answer</summary>

```bash
echo '{"name":"Alex","role":"cloud engineer"}' | jq -r .role
```

Output:

```
cloud engineer
```

`-r` means **raw**: plain text, no quotes.

</details>

---

## 🐛 Troubleshooting: "I have two copies of jq!"

Recent versions of macOS **already include jq** at `/usr/bin/jq`. After installing with Homebrew, you may have two copies.

**Check:**

```bash
which -a jq
jq --version
```

- `which -a` lists **every** copy, in the order your terminal checks them. The **first one wins**.
- Homebrew's copy (`/opt/homebrew/bin/jq`) should be first.

**If `jq --version` still shows the old Apple version** (e.g. `jq-1.7.1-apple`): your terminal remembered the old location. Fix it with:

```bash
rehash
jq --version
```

`rehash` makes the terminal forget the locations it remembered and look again.

**Lesson:** when a tool behaves unexpectedly, **check which version is actually running**. "Wrong version" is a very common real-world problem.

---

## 💰 Cost check (if you already have an AWS account)

Today costs nothing. If you've experimented with AWS before, though, it's worth a quick look:

1. Sign in at **console.aws.amazon.com**
2. Search **Billing** at the top, then open **Billing and Cost Management**
3. Check **Month-to-date cost**

Forgotten test resources are the most common cause of surprise cloud bills. (We set up automatic budget alerts later in the programme.)

---

## ✅ Day 1 checklist

- [ ] I can open Terminal and run basic commands (`pwd`, `ls`, `cd`, `mkdir`, `cat`)
- [ ] I can read permissions like `drwxr-xr-x` and `-rw-------`
- [ ] Homebrew is installed and `brew --version` works
- [ ] I found commands myself using `brew help`
- [ ] jq is installed and I can use `jq .key` and `jq -r .key`
- [ ] I know how to check which copy of a tool is running (`which -a`)

## Lessons learned

- **Read the output.** Most answers (and most errors) are right there on the screen.
- **Use built-in help** (`--help`, `help`, `man`) before searching online. It's faster and matches your version.
- **Check which version is running** when something behaves oddly.
- **Protect secrets.** Know where they live, check their permissions, and never share their contents.

## Next: Day 2, Git and GitHub

Set up a secure SSH connection to GitHub and create a learning repository to track every change.
