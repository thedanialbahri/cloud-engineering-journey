# Day 2: Git, GitHub and SSH

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

## 🤖 Using AI in cloud engineering: a tool, not a crutch

AI assistants are now common in cloud teams. They're useful, but treat them like any other tool: something you **check**, not something you **trust blindly**.

**What AI is good for:**
- Explaining an error message or an unfamiliar command
- Drafting a first version of a script, Terraform file or document
- Reviewing work and spotting things you missed
- Speeding up repetitive tasks you already understand

**Why you shouldn't rely on it:**
- **It can be confidently wrong**, especially about newer tool versions.
- **Cloud mistakes cost money or cause security problems.** A wrong `terraform destroy`, an open security group or a leaked key is your responsibility, not the AI's.
- **You have to understand it to check it.** If you can't explain what a command does, don't run it.
- **Incidents and interviews need you.** When something breaks at 2am, you need to know how to troubleshoot it.

**Rules for using AI safely:**

| Rule | Why |
|---|---|
| Understand before running | Know what each part of a command does |
| Never paste secrets | No passwords, keys, tokens, recovery codes or private keys |
| Verify the result | Check with `git status`, `ls`, `terraform plan` and so on |
| Use the official docs | AWS, Terraform and GitHub docs are the source of truth |
| AI suggests, you decide | Anything that changes or deletes real resources is your call |
| Do the core work yourself while learning | That's how the skills stick |

**Example:** an AI review can spot that your Git email is your personal address instead of your noreply one. But you still need to understand **why** it matters (it's public on every commit) and make the fix yourself.

---

## ✅ Day 2 checklist

- [ ] I can explain repo, commit, branch and remote in plain English
- [ ] 2FA is on, with recovery codes saved
- [ ] Git uses my noreply email
- [ ] I have an SSH key with a passphrase, added to GitHub, and `ssh -T` works
- [ ] My repo has a `.gitignore`, a README and meaningful commits
- [ ] I've pushed to GitHub and merged my own pull request

## Lessons learned

- **Commit ≠ push.** Committing saves locally. Pushing sends to GitHub.
- **Read warnings.** Git often tells you exactly what's wrong.
- **Check, act, check again.** Run `pwd`, `ls` or `git status` before and after changes.
- **Protect secrets from day one.** Use `.gitignore`, private emails and passphrases.

## Next: Day 3

Continue setting up the engineering workstation and connect an AI coding assistant safely, with rules that keep it away from secrets.
