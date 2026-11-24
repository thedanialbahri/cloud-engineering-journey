# Day 2: Git, GitHub, SSH and a Safe AI Assistant

> Written so a complete beginner can follow along on their own machine.

**Time needed:** about 2–3 hours
**Cost:** £0 (nothing runs in the cloud today)
**Works on:** macOS. Most steps also work on Linux.

---

## What you'll learn

| Thing | In one line |
|---|---|
| Git | Saves snapshots of your project so you can see what changed and go back |
| GitHub | Stores your Git projects online, so you can share them and work with others |
| SSH key | A secure way to prove who you are to GitHub, without typing a password |
| `.gitignore` | A list of files Git must never save, such as secrets |
| Pull request | The way teams review and approve changes before they go live |
| Claude Code safety | Rules and locks that stop an AI assistant touching your secrets |

## Why this matters for cloud engineering

Cloud teams keep everything in Git: Terraform files, scripts, documentation. Every change to real infrastructure usually goes through a branch and a pull request, so someone can check it first. You'll use these skills every day.

---

## Step 1: Git in plain English

| Word | Meaning | Comparison |
|---|---|---|
| **Repo** | A project folder Git is tracking. The history lives in a hidden `.git` folder. | A photo album for one project |
| **Commit** | A saved snapshot of your files, with a message | One photo, with a caption |
| **Branch** | A separate line of work, so `main` stays safe | A draft copy |
| **Remote** | A copy of the repo somewhere else, usually GitHub. Its nickname is `origin`. | A backup of the album in the cloud |

The everyday flow:

```
edit files  →  git add  →  git commit  →  git push
 (change)      (choose)     (snapshot)     (send to GitHub)
```

**Commit vs push:** a commit saves a snapshot **on your computer only**. A push sends your commits to GitHub. Committing does not touch GitHub at all.

A file can be in three states, and `git status` tells you which one:

```
Untracked   →   Staged          →   Committed
(new file)      (git add)           (git commit)
```

**Tip:** `git status` only looks and never changes anything, so it's always safe to run. Run it often.

---

## Step 2: Secure your GitHub account

1. Turn on **2FA** (two-factor authentication). Use an **authenticator app**, not SMS, because phone numbers can be stolen ("SIM swapping").
2. Save your **recovery codes** somewhere safe. They're your only way back in if you lose your phone.
3. Use **two different emails**:

| Job | Which email | Who sees it |
|---|---|---|
| Logging in to GitHub | Your personal email | Only you and GitHub |
| Signing your commits | Your GitHub **noreply** email | **Everyone**, it's on every public commit |

Find your noreply email in **GitHub → Settings → Emails**. While you're there, tick:
- **Keep my email addresses private**
- **Block command line pushes that expose my email**

Then tell Git to use it:

```bash
git config --global user.email "ID+username@users.noreply.github.com"
git config --global user.email   # check it
```

> ⚠️ **Never** share recovery codes, passwords or 2FA codes with anyone, including AI tools.

---

## Step 3: Create an SSH key

An SSH key is a **pair** of files:

| File | Comparison | Rule |
|---|---|---|
| `id_ed25519.pub` (public) | A padlock | Give it to GitHub. Safe to share. |
| `id_ed25519` (private) | The only key that opens the padlock | **Never** share, paste or commit it |

**1. Check you don't already have a key** (so you don't overwrite one):

```bash
ls -la ~/.ssh
```

**2. Create the pair:**

```bash
ssh-keygen -t ed25519 -C "your-noreply-email"
```

| Part | Meaning |
|---|---|
| `-t ed25519` | The **t**ype of key. Modern, secure, and recommended by GitHub. |
| `-C "..."` | A **c**omment (label) so you can recognise the key later |

Press Enter to accept the default location, then set a **passphrase**. Nothing appears on screen while you type it. That's normal.

**3. Check the permissions:**

```bash
ls -la ~/.ssh
```

The private key should be `-rw-------` (only you can read it). SSH refuses to use a private key that other people can read.

**4. Copy the public key and add it to GitHub:**

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

> ⚠️ Make sure the file name ends in **`.pub`**.

Then go to **GitHub → Settings → SSH and GPG keys → New SSH key**, give it a title (such as your computer's name) and paste.

**5. Test it:**

```bash
ssh -T git@github.com
```

The first time, it asks whether you trust GitHub's fingerprint. Compare it with GitHub's official **"GitHub's SSH key fingerprints"** docs page, then type `yes` in full.

Success looks like:

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

**6. Optional: stop typing the passphrase every time (macOS)**

Create `~/.ssh/config` with:

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

---

## Step 4: Create your repo

```bash
mkdir -p ~/projects/my-repo
cd ~/projects/my-repo
git init
git status
```

You should see `No commits yet`.

### Add `.gitignore` first, before anything else

Bots scan GitHub for leaked keys within minutes, so set this up before adding any other files:

```
# Mac system files
.DS_Store

# Secrets - never commit
.env
*.pem
*.key

# Terraform
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
```

| Line | Meaning |
|---|---|
| `#` | A note for humans. Git skips it. |
| `*` | "Anything", so `*.key` means any file ending in `.key` |
| `*.tfstate` | Terraform's record of your cloud. It can contain secrets. |

**Test it:** if `ls -la` shows a `.DS_Store` file but `git status` doesn't, your `.gitignore` is working.

### Make your first commits, one idea per commit

```bash
git add .gitignore
git commit -m "Add .gitignore to keep secrets and Terraform files out of Git"
git log --oneline
```

| ✅ Good message | ❌ Bad message |
|---|---|
| `Add README with intro and roadmap` | `update` |
| Starts with a verb and says **what** and **why** | Tells you nothing |

### Push to GitHub

Create an **empty** repo on GitHub (don't tick README, `.gitignore` or licence), then:

```bash
git remote add origin git@github.com:username/my-repo.git
git remote -v
git push -u origin main
```

---

## Step 5: Branch and pull request

Teams never change `main` directly. Instead:

```
branch → commit → push → pull request → merge → pull
```

```bash
git switch -c add-new-guide            # create a branch and move onto it
git add docs
git commit -m "Add new guide"
git push -u origin add-new-guide       # send the branch to GitHub
```

On GitHub: **Compare & pull request → Create pull request → Files changed** (review it) **→ Merge pull request → Confirm merge**.

Then bring the change back to your computer:

```bash
git switch main
git pull
git log --oneline
git branch -d add-new-guide
```

> ⚠️ **Check the merge really happened** before deleting your branch. If `git pull` says `Already up to date` straight after a merge, or Git warns `not yet merged`, stop and check the pull request on GitHub.

---

## 🐛 Troubleshooting: common problems

| Problem | Clue | Fix |
|---|---|---|
| File saved as `.gitignore.save` | nano closed in an unusual way | Check it with `cat`, then rename: `mv .gitignore.save .gitignore` |
| Working in the wrong folder | `pwd` shows a subfolder, or `git status` shows `../` (one folder up) | `cd ..`, then check for `.git` with `ls -la` |
| `readme.md` instead of `README.md` | `git status` shows lowercase | `mv readme.md temp.md` then `mv temp.md README.md` |
| `cp` says `No such file or directory` | You used a search term, not a path | Use the full path, for example from `mdfind -name <text>` |
| Empty folder doesn't appear in Git | `git status` says nothing about it | Git tracks files, not folders. Add a file to it. |

**Big lesson: macOS ignores capital letters in file names, Linux doesn't.** Cloud servers run Linux, so `readme.md` and `README.md` are two different files there. This is a classic "works on my machine" bug.

**Useful commands:**

| Command | What it does |
|---|---|
| `git rev-parse --show-toplevel` | Shows the repo's top folder, from anywhere inside it |
| `mdfind -name <text>` | Searches your Mac by file name |
| `mv` vs `cp` | Move (one copy left) vs copy (two copies) |
| `rm -rf <path>` | Deletes for good, with no undo. Check with `pwd` and `ls` first. |

---

## Step 6: Set up an AI assistant safely (Claude Code)

AI assistants like **Claude Code** can read your files and run commands inside your project. That's handy, but it also means they could read a secret by accident, or run something that costs money. So before using one, give it **rules** and **locks**.

Real-life example: you've hired a cleaner.

| Layer | File | Real-life version |
|---|---|---|
| 📝 Instructions | `CLAUDE.md` | A note on the fridge: "Please don't go in the office". Usually followed, but not forced. |
| 🔒 Locks | `.claude/settings.json` | Actually locking the office and the safe. Enforced by Claude Code. |
| 👀 You | Permission prompts | Checking what the cleaner wants to do before you say yes |

### 6a. Write the instructions: `CLAUDE.md`

Create `CLAUDE.md` in the top of your repo. Keep it short and clear, for example:

```
# Rules for Claude in this repo
- Explain every command simply, with a real-life example.
- Give hints first when something is new.
- Never run terraform apply or terraform destroy.
- Never create, change or delete AWS resources.
- Never read ~/.aws, ~/.ssh, .env, *.tfstate or *.tfvars files, or private keys.
```

### 6b. Fit the locks: `.claude/settings.json`

```bash
mkdir -p .claude
nano .claude/settings.json
```

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "deny": [
      "Read(~/.aws/**)",
      "Read(~/.ssh/**)",
      "Read(.env)",
      "Read(.env.*)",
      "Read(*.tfstate)",
      "Read(*.tfstate.*)",
      "Read(*.tfvars)",
      "Read(*.pem)",
      "Read(*.key)",
      "Bash(terraform apply *)",
      "Bash(terraform destroy *)",
      "Bash(aws *)",
      "Bash(* ~/.ssh*)",
      "Bash(* ~/.aws*)",
      "Bash(* $HOME/.ssh*)",
      "Bash(* $HOME/.aws*)"
    ]
  }
}
```

| Rule | What it blocks |
|---|---|
| `Read(~/.aws/**)`, `Read(~/.ssh/**)` | Claude reading your secret folders (`**` = everything inside) |
| `Read(.env)`, `Read(*.pem)`, `Read(*.key)`… | Claude reading files that usually hold passwords or keys |
| `Read(*.tfstate)`, `Read(*.tfvars)` | Claude reading Terraform files that can hold secrets |
| `Bash(terraform apply *)`, `Bash(terraform destroy *)` | Building or deleting cloud resources |
| `Bash(aws *)` | Any AWS command at all |
| `Bash(* ~/.ssh*)` and friends | Using a terminal command (like `cat`) to peek into secret folders |

**Check the file has no typos:**

```bash
jq . .claude/settings.json
```

If `jq` prints it neatly, it's valid. If it shows an error, look for a missing or extra comma. One mistake and Claude Code ignores the file.

### 6c. Test the lock (don't just assume)

Open the repo in VS Code, start Claude Code, and ask it to **try** reading `~/.ssh/id_ed25519.pub`. Use the **public** key on purpose: if the lock fails, nothing secret leaks.

**Pass:**

```
File is in a directory that is denied by your permission settings.
```

> 💡 If Claude simply **refuses** because of `CLAUDE.md`, that tests the note, not the lock. Tell it this is a one-off test of your deny rules and ask it to try, so you can see the lock in action.

**Honest limit:** these rules match the words of a command, so an unusual command could still get past. That's why you stay the last line of defence: read every permission prompt, and never choose "don't ask again" for anything near your secrets.

For more on how AI fits into learning cloud engineering, see [How AI Is Used in This Guide](how-ai-is-used.md).

---

## ✅ Day 2 checklist

- [ ] I can explain repo, commit, branch and remote in plain English
- [ ] 2FA is on, with recovery codes saved
- [ ] Git uses my noreply email
- [ ] I have an SSH key with a passphrase, added to GitHub, and `ssh -T` works
- [ ] My repo has a `.gitignore`, a README and meaningful commits
- [ ] I've pushed to GitHub and merged my own pull request
- [ ] Claude Code has a `CLAUDE.md` and a tested `.claude/settings.json`

## Lessons learned

- **Commit ≠ push.** Committing saves locally. Pushing sends to GitHub.
- **Read warnings.** Git often tells you exactly what's wrong.
- **Check, act, check again.** Run `pwd`, `ls` or `git status` before and after changes.
- **Protect secrets from day one.** Use `.gitignore`, private emails and passphrases.
- **Test your locks.** A rule you haven't tested is only a guess.

New word or command you don't recognise? See the [Commands and Jargon Reference](reference.md).

## Next: Day 3

More workstation setup, and the first steps towards working with AWS safely.
