# Commands and Jargon Reference

Every command and term used in this guide so far, explained simply. Think of it as a **command library**: come back here whenever you need a command again, or see a word you don't recognise.

Commands are grouped by topic, not by day. New commands are added here as each guide is written.

**Tip:** press `Cmd + F` (or `Ctrl + F`) to search this page.

---

## Contents

- [Terminal basics](#terminal-basics)
- [Files and folders](#files-and-folders)
- [Permissions](#permissions)
- [Finding things](#finding-things)
- [Shell settings: zsh and .zshrc](#shell-settings-zsh-and-zshrc)
- [Homebrew](#homebrew)
- [JSON and jq](#json-and-jq)
- [The nano editor](#the-nano-editor)
- [Clipboard](#clipboard)
- [Git: the ideas](#git-the-ideas)
- [Git: the commands](#git-the-commands)
- [GitHub](#github)
- [SSH keys](#ssh-keys)
- [Security words](#security-words)
- [Claude Code and AI safety](#claude-code-and-ai-safety)
- [Cloud and Terraform words](#cloud-and-terraform-words)
- [AWS CLI commands](#aws-cli-commands) (coming soon)
- [Terraform commands](#terraform-commands) (coming soon)

---

## Terminal basics

| Term | What it means | Real-life example |
|---|---|---|
| **Terminal** | The app where you type commands to control your computer | Texting your computer instructions instead of clicking |
| **Shell** | The program inside the terminal that reads your commands. On a Mac it's usually **zsh**. | The person reading your texts and doing what they say |
| **Command** | One instruction, like `ls` or `cd` | One item on a to-do list |
| **Option / flag** | Extra letters that change what a command does, like `-l` or `-p` | "Coffee, **with milk**" — same order, slightly different |
| **Home folder** (`~`) | Your own main folder, e.g. `/Users/yourname` | Your own bedroom in a shared house |
| **Path** | The full address of a file or folder | A postal address: country → town → street → house |
| **PATH** | The list of folders the terminal checks when you type a command | The list of shops you check, in order, when looking for something |
| **Hidden file** | A file whose name starts with a dot, like `.git` or `.zshrc`. Usually settings. | Things kept in a drawer instead of on the table |
| **Output** | What a command prints back | The reply to your text |

| Command | What it does | Example |
|---|---|---|
| `pwd` | Shows which folder you're in ("print working directory") | `pwd` → `/Users/you/projects` |
| `cd folder` | Moves into a folder | `cd projects` |
| `cd ..` | Moves **up** one folder | From `.../docs` back to the repo |
| `cd ~` | Goes straight to your home folder | Like pressing "Home" |
| `echo text` | Prints text | `echo hello` → `hello` |
| `echo $SHELL` | Shows which shell you're using | → `/bin/zsh` |
| `echo $PATH` | Shows your PATH: the folders checked for commands, in order, split by `:` | → `/opt/homebrew/bin:/usr/bin:...` |
| `uname -m` | Shows your chip type | `arm64` = Apple Silicon |
| `sw_vers` | Shows your macOS version | → `ProductVersion: 15.1` |
| `tool --version` | Checks a tool is installed and shows its version | `git --version`, `jq --version` |
| `tool --help` / `man tool` | Shows the built-in help for a tool. In `man`, press `q` to quit. | `jq --help`, `man ls` |
| `cmd1 && cmd2` | Runs the second command **only if** the first worked | `mkdir lab && cd lab` |
| `cmd1 \| cmd2` | A **pipe**: sends the output of one command into another | `echo '{"a":1}' \| jq .a` |
| `cmd > file` | Writes the output into a file, **replacing** what's there | `echo hello > test.txt` |
| `cmd >> file` | Adds the output to the **end** of a file, keeping what's there | `echo world >> test.txt` |
| `rehash` | Makes zsh forget where it last found commands and look again | Use after installing a new version of a tool |

**`>` vs `>>`** — get this wrong and you can wipe a file.

| Symbol | What it does | Real-life example |
|---|---|---|
| `>` | **Replaces** the whole file with the new text | Tearing out every page of a notebook and writing one new line |
| `>>` | **Adds** the new text to the end | Writing a new line at the bottom of the page |

**Tip:** before using `>` or `>>` on an important file, make a backup with `cp` (see [Files and folders](#files-and-folders)).

---

## Files and folders

| Command | What it does | Example | Real-life example |
|---|---|---|---|
| `ls` | Lists what's in a folder | `ls` | Looking around a room |
| `ls folder` | Lists what's in **another** folder, without moving into it | `ls docs` | Peeking into the next room from the doorway |
| `ls -l file` | Shows details (permissions, owner, size, date) for one file | `ls -l test.txt` | Reading the label on one box |
| `ls -la` | Lists **everything**, including hidden files, with details | `ls -la` | Opening every drawer too |
| `mkdir name` | Makes a folder | `mkdir docs` | Getting a new box |
| `mkdir -p a/b` | Makes a folder **and** any missing folders above it | `mkdir -p ~/projects/my-repo` | Building the shelf and the box in one go |
| `cat file` | Shows what's inside a file | `cat README.md` | Reading a page out loud |
| `cp from to` | **Copies** a file (you end up with two) | `cp notes.md docs/` | Photocopying a page |
| `cp file file.backup` | Makes a backup copy next to the original, before you change it | `cp ~/.zshrc ~/.zshrc.backup` | Photocopying a form before you fill it in |
| `mv from to` | **Moves** a file, or renames it (you end up with one) | `mv old.md new.md` | Moving a page to a new folder |
| `rm -rf path` | Deletes a folder and everything in it. **No undo, no bin.** | `rm -rf ~/old-folder` | A shredder. Check twice with `pwd` and `ls` first. |

**`-r`** = recursive (everything inside too). **`-f`** = force (don't ask).

**Undo a backup change:** if you break a file, copy the backup back over it: `cp ~/.zshrc.backup ~/.zshrc`.

**Case-sensitive** means capital letters matter. Your Mac treats `readme.md` and `README.md` as the **same** file. Linux (which cloud servers run on) treats them as **different** files. Always use the exact name.

**Fixing the case of a file name on a Mac:** rename it in two steps, through a temporary name, so the Mac notices the change.

```
mv readme.md temp.md
mv temp.md README.md
```

---

## Permissions

Every file and folder has rules about **who can do what**. You see them with `ls -l` or `ls -la`.

```
d    rwx    r-x    r-x
│    YOU    GROUP  EVERYONE ELSE
└── d = folder, - = file
```

| Letter | Meaning |
|---|---|
| `d` (first) | It's a folder |
| `-` (first) | It's a file |
| `r` | Can **read** |
| `w` | Can **write** (change) |
| `x` | Can **enter** (folder) or **run** (file) |
| `-` (anywhere else) | Not allowed |

| Example | Plain English |
|---|---|
| `drwxr-xr-x` | A folder. You can do anything. Others can look but not change. |
| `-rw-r--r--` | A file. You can read and change it. Others can only read it. |
| `-rw-------` | A file only you can read and change. ✅ The right setting for secrets. |

**Tip:** always check the **first character** first.

---

## Finding things

| Command | What it does | Example |
|---|---|---|
| `which tool` | Shows **which copy** of a tool runs when you type its name | `which brew` → `/opt/homebrew/bin/brew` |
| `which -a tool` | Shows **every** copy, in the order they're checked. The first one wins. | `which -a jq`, `which -a brew` |
| `mdfind -name text` | Searches your Mac by file name (uses Spotlight) | `mdfind -name day-01` |
| `git rev-parse --show-toplevel` | Shows the top folder of the Git repo you're in | Works from any folder inside the repo |

**Remember:** a search word isn't a path. `mdfind` prints the full path, and that's what you give to `cp` or `mv`.

**Same path listed twice?** If `which -a brew` shows `/opt/homebrew/bin/brew` **twice**, it's the same copy, but that folder is in your PATH twice. It's harmless, but messy. The fix is `typeset -U path` (see [Shell settings](#shell-settings-zsh-and-zshrc)).

---

## Shell settings: zsh and .zshrc

**`~/.zshrc`** is a hidden settings file in your home folder. zsh reads it **every time you open a new terminal window**. Real-life example: a **morning checklist** your shell follows before it starts work.

| Command | What it does | Example | Real-life example |
|---|---|---|---|
| `cat ~/.zshrc` | Shows what's in your settings file | `cat ~/.zshrc` | Reading the checklist |
| `nano ~/.zshrc` | Opens the settings file to edit by hand | `nano ~/.zshrc` | Rewriting the checklist with a pen |
| `cp ~/.zshrc ~/.zshrc.backup` | Backs up the settings file before you change it | Do this first, every time | Photocopying the checklist first |
| `echo "line" >> ~/.zshrc` | Adds one line to the **end** of the settings file. Use `>>`, never `>`. | `echo "typeset -U path" >> ~/.zshrc` | Adding one item to the bottom of the checklist |
| `source ~/.zshrc` | Re-reads the settings file **now**, so changes work in this window | Run after every change | Re-reading the checklist after updating it, instead of waiting until tomorrow |
| `typeset -U path` | Tells zsh to keep only **one** copy of each folder in PATH. `-U` = unique. `path` (small letters) is zsh's list version of `PATH`. | Put it in `~/.zshrc` | Crossing out duplicate shops on your shopping route |

**Safe routine for changing `~/.zshrc`:**

```
cp ~/.zshrc ~/.zshrc.backup      # 1. back up
echo "typeset -U path" >> ~/.zshrc  # 2. add a line
cat ~/.zshrc                     # 3. check it looks right
source ~/.zshrc                  # 4. load it
```

### Aliases (shortcuts)

An **alias** is a short nickname for a longer command. Real-life example: saving a contact as **"Mum"** instead of typing the full phone number every time.

| Command | What it does |
|---|---|
| `alias gs='git status'` | Makes `gs` mean `git status`, **in this window only** |
| `echo "alias gs='git status'" >> ~/.zshrc` | Saves the alias in your settings file, so it works in **every** new window |
| `alias` | Lists all the aliases you have |

**Why two kinds of quote marks?** The outer `"double quotes"` wrap the whole line for `echo`. The inner `'single quotes'` wrap the command the alias stands for. If you used the same kind for both, the shell would get confused about where each one ends.

**Aliases used in this guide:**

| Alias | Stands for | What it does |
|---|---|---|
| `gs` | `git status` | Shows what's changed |
| `ga` | `git add` | Stages files, e.g. `ga docs` |
| `gl` | `git log --oneline` | Shows the commit history, one line each |

To add all three:

```
echo "alias gs='git status'" >> ~/.zshrc
echo "alias ga='git add'" >> ~/.zshrc
echo "alias gl='git log --oneline'" >> ~/.zshrc
source ~/.zshrc
```

Test them with `gs` and `gl` inside a Git repo.

---

## Homebrew

**Homebrew** is a tool that installs other tools on a Mac. Real-life example: an **app store for the terminal**.

| Term / command | What it means | Example |
|---|---|---|
| **Package** | A tool you can install | `jq` is a package |
| **Dependency** | Another package a tool needs to work | jq needs `oniguruma` |
| **Bottle** | A ready-made package built for your type of Mac | Like a ready meal instead of cooking from scratch |
| `brew help` | Shows Homebrew's main commands | |
| `brew search name` | Looks for a package | `brew search jq` |
| `brew info name` | Shows details about a package without installing | `brew info jq` |
| `brew install name` | Installs a package | `brew install jq` |
| `brew --version` | Checks Homebrew is installed | |

---

## JSON and jq

**JSON** is a way of writing information so computers can read it, as **labels** and **values**. AWS replies to almost everything in JSON.

```json
{"name": "web-1", "status": "running"}
```

Real-life example: a **form** with boxes filled in. `name` is the box label, `web-1` is what's written in it.

| Symbol | Meaning |
|---|---|
| `{ }` | A group of labels and values |
| `[ ]` | A list |
| `"name": "web-1"` | One label and its value |

**jq** picks out the bits of JSON you ask for. Real-life example: asking a **librarian** for one specific page. They don't guess what you want; you tell them.

| Command | What it does | Example output |
|---|---|---|
| `jq . file.json` | Prints the whole file neatly. If there's a mistake in the JSON, it shows an error instead. Great for checking a file. | The file, tidied up |
| `jq .name` | Pulls out one value | `"web-1"` |
| `jq -r .name` | Same, without the quote marks (**r**aw) | `web-1` |
| `jq -r '.servers[].name'` | Gets the `name` from **every** item in a list | `web-1` `web-2` `db-1` |

---

## The nano editor

**nano** is a simple text editor that runs inside the terminal.

| Keys | What it does |
|---|---|
| `nano file` | Opens (or creates) a file |
| `Ctrl + O`, then `Enter` | **Saves**. Look for `Wrote ... lines` at the bottom. |
| `Ctrl + X` | Exits |

⚠️ `Ctrl` is the **Control** key, not `Cmd`. If you exit without saving, your changes are lost. If nano is closed unexpectedly, it may rescue your work as `filename.save`.

---

## Clipboard

| Command | What it does | Example |
|---|---|---|
| `pbcopy < file` | Copies a file's contents to the clipboard, without showing them | `pbcopy < ~/.ssh/id_ed25519.pub` |
| `pbpaste` | Prints whatever is on the clipboard | `pbpaste > settings.json` puts the clipboard into a file |

**Tip:** with `pbpaste`, make sure the **right thing** is on the clipboard. If you copied the command last, that's what gets pasted.

---

## Git: the ideas

**Git** saves snapshots of your project over time, so you can see what changed and go back. Real-life example: a **photo album** of your project.

| Term | What it means | Real-life example |
|---|---|---|
| **Repo** (repository) | A folder Git is tracking. The history lives in a hidden `.git` folder. | The photo album |
| **Commit** | A saved snapshot, with a message | One photo with a caption |
| **Commit message** | A short note on what changed and why. Start with a verb: "Add…", "Fix…". | The caption |
| **Commit ID** | A unique code for each commit, like `69c99de` | The photo's number |
| **Staging** | Choosing which changes go into the next commit (`git add`) | Picking which photos go on the next page |
| **Untracked** | A new file Git isn't looking after yet | A new photo not in the album |
| **Modified** | A file Git knows about that has changed | A photo you've edited |
| **Working tree clean** | Everything is saved. Nothing waiting. | Every photo is in the album |
| **Branch** | A separate line of work, so `main` stays safe | A draft copy of a document |
| **main** | The main branch, the "real" version | The final, approved document |
| **HEAD** | Where you are right now in the history | "You are here" on a map |
| **Remote** | A copy of the repo somewhere else, usually GitHub | A backup of the album in the cloud |
| **origin** | The usual nickname for your GitHub copy | "Home" in your contacts list |
| **Push** | Send your commits **to** GitHub | Uploading photos |
| **Pull** | Get new commits **from** GitHub | Downloading photos someone else added |
| **Merge** | Combine a branch's changes into another branch | Adding the draft's changes into the final document |
| **`.gitignore`** | A list of files Git must never save, such as secrets | A "do not pack" list for a suitcase |

**Commit vs push:** a commit saves on **your computer only**. A push sends it to GitHub.

```
edit  →  git add  →  git commit  →  git push
```

---

## Git: the commands

| Command | What it does | Example |
|---|---|---|
| `git --version` | Checks Git is installed | → `git version 2.x` |
| `git init` | Turns a folder into a Git repo | Run once per project |
| `git status` | Shows what's going on: changed files, branch, anything waiting. **Safe to run any time.** | Run it before and after everything |
| `git add file` | Stages a file (puts it on the ready-to-save list) | `git add README.md` |
| `git add folder` | Stages everything in a folder | `git add docs` |
| `git commit -m "msg"` | Saves a snapshot with a message | `git commit -m "Add Day 1 guide"` |
| `git log --oneline` | Lists commits, one per line, newest first | |
| `git switch -c branch-name` | **C**reates a new branch and moves onto it | `git switch -c add-new-guide` |
| `git switch name` | Moves to an existing branch | `git switch main` |
| `git branch` | Lists branches. `*` shows where you are. | |
| `git branch -d name` | **D**eletes a finished branch. Warns you if its work isn't merged. | `git branch -d add-new-guide` |
| `git remote add origin URL` | Saves your GitHub address as `origin` | |
| `git remote -v` | Shows saved addresses | |
| `git push -u origin main` | Sends `main` to GitHub. `-u` remembers it, so next time just `git push`. | |
| `git push -u origin branch` | Sends a branch to GitHub | |
| `git pull` | Downloads new commits from GitHub | |
| `git config --global user.email "x"` | Sets the email on your commits | Use your GitHub noreply email |
| `git config --global user.email` | Shows the email currently set (no value = just check) | |

**Shortcuts:** `gs`, `ga` and `gl` save typing for the Git commands you use most. See [Aliases](#aliases-shortcuts).

**The everyday branch routine:**

```
git switch main
git pull                          # get the latest main
git switch -c add-new-guide       # new branch for your work
# ...edit files...
git add docs
git commit -m "Add new guide"
git push -u origin add-new-guide  # then open a pull request on GitHub
```

After the pull request is merged on GitHub: `git switch main`, `git pull`, then `git branch -d add-new-guide`.

**Reading Git's messages:**

| Message | Meaning |
|---|---|
| `fatal: not a git repository` | You're not inside a repo |
| `Reinitialized existing Git repository` | `git init` was already run here. Harmless. |
| `Already up to date` | Nothing new to download. If you expected changes, check the merge really happened. |
| `not yet merged` warning | Stop! That branch's work isn't in your current branch yet. |

---

## GitHub

**GitHub** is a website that stores Git repos online, so you can back them up, share them and work with others.

| Term | What it means | Real-life example |
|---|---|---|
| **Pull request (PR)** | A request to merge your branch into `main`, so it can be checked first | Handing in a draft and saying "can you check this before it goes in the final version?" |
| **Files changed** | The tab in a PR showing what's different. Green = added, red = removed. | Tracked changes in a document |
| **Merge pull request → Confirm merge** | Two buttons. The merge only happens after **Confirm**. | Signing, then posting the letter |
| **Merged** (purple label) | The PR's changes are now in `main` | The letter has been delivered |
| **README** | The front page of a repo, shown automatically | The cover of a book |
| **Markdown** (`.md`) | Plain text with simple symbols for formatting: `#` heading, `-` bullet, `` ` `` code | Notes with a few highlighter rules |
| **Public repo** | Anyone can see it | A shop window |

---

## SSH keys

**SSH** is a secure way for your computer to prove who it is to another computer, like GitHub, without a password.

| Term | What it means | Real-life example |
|---|---|---|
| **Key pair** | Two files made together: a public key and a private key | A padlock and its only key |
| **Public key** (`.pub`) | Safe to share. You give it to GitHub. | The padlock |
| **Private key** | Never shared, never pasted, never committed | The key that opens the padlock |
| **Passphrase** | A password on the private key file itself | A lockbox code for your key |
| **ed25519** | A modern, secure type of key | A newer, stronger lock design |
| **Fingerprint** | A short ID for a key, like `SHA256:...` | The serial number on a lock |
| **Known hosts** | A list of computers your Mac has already checked and trusts | Your saved contacts |
| **SSH agent** | A helper that holds your unlocked key while you work | A trusted doorman holding your key |
| **Keychain** | The Mac's built-in password safe | A safe for your codes |
| **`~/.ssh/config`** | A settings file for SSH | Instructions for the doorman |

| Command | What it does |
|---|---|
| `ls -la ~/.ssh` | Lists your SSH files and their permissions. The private key should be `-rw-------`. |
| `ssh-keygen -t ed25519 -C "label"` | Makes a new key pair. `-t` = type, `-C` = label. |
| `ssh-keygen -c -C "new label" -f file` | **C**hanges the label on an existing key |
| `ssh-keygen -y -f file` | Asks for the passphrase if one is set. A good way to check you set one. |
| `ssh -T git@github.com` | Tests the connection to GitHub. Success: `Hi username!` |
| `ssh-add --apple-use-keychain file` | Gives the key to the agent and saves the passphrase in Keychain |
| `ssh-add -l` | **L**ists keys the agent is holding |
| `nano ~/.ssh/config` | Opens the SSH settings file to edit |
| `pbcopy < ~/.ssh/id_ed25519.pub` | Copies the **public** key to paste into GitHub. Never do this with the private key. |

---

## Security words

| Term | What it means | Real-life example |
|---|---|---|
| **2FA** (two-factor authentication) | Logging in needs your password **and** a code from your phone | A door with a key **and** a PIN pad |
| **Authenticator app** | An app that makes the 2FA codes. Safer than SMS. | |
| **SIM swapping** | A criminal moves your phone number to their SIM to steal SMS codes | Someone redirecting your post |
| **Recovery codes** | Backup codes to get back in if you lose your phone | A spare key with a trusted neighbour |
| **noreply email** | A private GitHub email for your commits, so your real email stays hidden | A PO box instead of your home address |
| **Secret** | Anything that gives access: passwords, keys, tokens | House keys |
| **Least privilege** | Give people and tools only the access they need | A cleaner gets a key to the house, not the safe |

---

## Claude Code and AI safety

| Term | What it means | Real-life example |
|---|---|---|
| **Claude Code** | An AI assistant that can read files and run commands inside a project | A capable cleaner working in your house |
| **`CLAUDE.md`** | Instructions for Claude in this repo | A note on the fridge. Polite, not forced. |
| **`.claude/settings.json`** | Rules Claude Code **enforces** | Locked doors |
| **Deny rule** | A rule that blocks an action | "This door stays locked" |
| `Read(~/.ssh/**)` | Blocks reading anything in the SSH folder. `**` = everything inside, at any depth. | Locking a whole room |
| `Bash(aws *)` | Blocks any terminal command starting with `aws`. `*` = anything after it. | "No using the credit card, for anything" |
| **Permission prompt** | Claude Code asking you before it does something | The cleaner asking "can I move this?" |
| **Sandboxing** | A stronger way to limit what commands can reach (covered later) | A room with no windows |

---

## Cloud and Terraform words

These come up properly in later guides. For now, just know what they are.

| Term | What it means | Real-life example |
|---|---|---|
| **AWS** (Amazon Web Services) | A cloud provider: you rent computers, storage and networks | Renting instead of buying |
| **AWS CLI** | The terminal tool for controlling AWS (`aws ...`) | A remote control for your cloud |
| **Terraform** | A tool that builds cloud setups from code files | A recipe that cooks the same meal every time |
| `terraform plan` | Shows what Terraform **would** change, without changing anything | Reading the recipe before cooking |
| `terraform apply` | Actually builds or changes things. **Can cost money.** | Cooking the meal |
| `terraform destroy` | Deletes what Terraform built | Clearing the table |
| **State file** (`.tfstate`) | Terraform's record of what it built. Can contain secrets. Never commit it. | The receipt for everything you bought |
| **`.tfvars`** | Terraform settings files. Sometimes contain secrets. | Your shopping list with your card number on it |
| **`.env`** | A file of settings that often includes passwords | A sticky note with passwords on it |

---

## AWS CLI commands

Coming soon. AWS commands are added here once the guide reaches AWS (after budget alerts are set up, so nothing costs money by surprise).

---

## Terraform commands

Coming soon. Terraform commands are added here once the guide reaches Terraform.
