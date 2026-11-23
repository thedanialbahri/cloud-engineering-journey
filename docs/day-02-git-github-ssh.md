# Day 2: Git, GitHub and SSH

**Goal:** set up Git and GitHub securely, create this repo, and make my first commits, push and pull request.

**Why it matters:** cloud teams keep everything (Terraform, scripts, docs) in Git. Branches, commits and pull requests are part of the daily job.

---

## 1. Git in plain English

| Word | Meaning | Comparison |
|---|---|---|
| **Repo** | A project folder Git is tracking. The history lives in a hidden `.git` folder. | A photo album for one project |
| **Commit** | A saved snapshot of my files, with a message | One photo, with a caption |
| **Branch** | A separate line of work, so `main` stays safe | A draft copy |
| **Remote** | A copy of the repo somewhere else (GitHub). Its nickname is `origin`. | A backup of the album in the cloud |

The everyday flow:

```
edit files  →  git add  →  git commit  →  git push
 (change)      (choose)     (snapshot)     (send to GitHub)
```

**Commit vs push:** a commit saves a snapshot on my Mac only. A push sends my commits to GitHub. Committing does not touch GitHub at all.

A file can be in three states, and `git status` tells me which:

```
Untracked   →   Staged          →   Committed
(new file)      (git add)           (git commit)
```

---

## 2. Securing my GitHub account

- **2FA** with an authenticator app (not SMS, because phone numbers can be stolen by "SIM swapping")
- **Recovery codes** saved somewhere safe, in case I lose my phone
- **Two different emails:**

| Job | Email | Who sees it |
|---|---|---|
| Logging in to GitHub | Personal email | Only me and GitHub |
| Signing commits (`git config user.email`) | GitHub **noreply** email | Everyone, it's on every public commit |

```bash
git config --global user.email "ID+username@users.noreply.github.com"
git config --global user.email   # check it
```

I also ticked **"Keep my email addresses private"** and **"Block command line pushes that expose my email"** in GitHub → Settings → Emails.

---

## 3. SSH key: proving it's me without a password

An SSH key is a **pair** of files:

| File | Comparison | Rule |
|---|---|---|
| `id_ed25519.pub` (public) | A padlock | Give it to GitHub. Safe to share. |
| `id_ed25519` (private) | The only key for that padlock | Never share, never paste, never commit |

```bash
ls -la ~/.ssh                                   # check for existing keys first
ssh-keygen -t ed25519 -C "your-noreply-email"   # make the pair, with a passphrase
pbcopy < ~/.ssh/id_ed25519.pub                  # copy the PUBLIC key
```

Then GitHub → Settings → SSH and GPG keys → New SSH key → paste.

**Test it:**

```bash
ssh -T git@github.com
# Hi username! You've successfully authenticated...
```

The first time, it asks if you trust GitHub's fingerprint. Check it against GitHub's official "SSH key fingerprints" docs page before typing `yes`.

**Permissions check:** the private key is `-rw-------` (only me). SSH refuses to use a private key that other people can read.

**Stop typing the passphrase every time:** add this to `~/.ssh/config` so macOS Keychain remembers it:

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

---

## 4. Creating the repo

```bash
mkdir -p ~/projects/cloud-engineering-journey
cd ~/projects/cloud-engineering-journey
git init
git status
```

### `.gitignore`: added first, before anything else

A list of files Git must never save. Bots scan GitHub for leaked keys within minutes, so this goes in before any other file.

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

**Proof it works:** `ls -la` showed a `.DS_Store` file, but `git status` didn't list it.

### First commits: one idea per commit

```bash
git add .gitignore
git commit -m "Add .gitignore to keep secrets and Terraform files out of Git"
git log --oneline
```

Good commit messages start with a verb and say **what** and **why**: `Add README with intro, roadmap and progress`, not `update`.

### Pushing to GitHub

```bash
git remote add origin git@github.com:username/cloud-engineering-journey.git
git remote -v
git push -u origin main
```

---

## 5. Problems I hit and how I fixed them

| Problem | Clue | Fix |
|---|---|---|
| `.gitignore` saved as `.gitignore.save` | nano closed in an unusual way, and `git status` showed `../.gitignore.save` | Checked the text with `cat`, renamed with `mv .gitignore.save .gitignore` |
| I was in the wrong folder | `pwd` showed `.../docs`, and `../` means "one folder up" | `cd ..`, then checked for `.git` with `ls -la` |
| Saved `readme.md` instead of `README.md` | `git status` showed lowercase | `mv readme.md temp.md` then `mv temp.md README.md` |
| `cp day-01-terminal docs/` failed | `No such file or directory` | A search term isn't a path. Use the full path that `mdfind` printed. |
| Empty `docs` folder didn't show in Git | `git status` said nothing | Git tracks files, not folders |

**Biggest lesson: macOS ignores capital letters in file names, Linux doesn't.** Cloud servers run Linux, so `readme.md` and `README.md` are different files there. A classic "works on my machine" bug.

**Useful commands:**

| Command | What it does |
|---|---|
| `git rev-parse --show-toplevel` | Shows the repo's top folder, from anywhere inside it |
| `mdfind -name <text>` | Searches the Mac by file name (Spotlight) |
| `mv` vs `cp` | Move (only one copy left) vs copy (two copies) |
| `rm -rf <path>` | Deletes for good, with no undo. Check with `pwd` and `ls` first. |

---

## 6. Using AI in cloud engineering: a tool, not a crutch

I use AI (such as Claude) while learning, and cloud teams use it at work too. I treat it like any other tool in my toolkit: useful, but something I check rather than trust blindly.

**What AI is good for:**
- Explaining an error message or a command I haven't seen before
- Drafting a first version of a script, Terraform file or document
- Reviewing my work and spotting things I missed
- Speeding up repetitive tasks once I already understand them

**Why I don't rely on it:**
- **It can be confidently wrong.** It can suggest commands or settings that look right but aren't, especially for newer tool versions.
- **In the cloud, mistakes cost money or cause security problems.** A wrong `terraform destroy`, an open security group or a leaked key is my responsibility, not the AI's.
- **I have to understand it to check it.** If I can't explain what a command does, I shouldn't run it.
- **Interviews and incidents need me, not the AI.** When something breaks at 2am, I need to know how to troubleshoot it.

**My rules for using AI safely:**

| Rule | Why |
|---|---|
| Understand before running | Ask what each part of a command does first |
| Never paste secrets | No passwords, AWS keys, recovery codes or private keys into any AI tool |
| Verify the result | Check the output with `git status`, `ls`, `terraform plan` and so on, rather than assuming it worked |
| Check the official docs | AWS, Terraform and GitHub docs are the source of truth |
| AI suggests, I decide | Anything that changes or deletes real resources is my decision |
| Keep doing the core work myself | While learning, I do my own Git work so the skills stick |

Example from today: AI helped me spot that my Git email was my personal address, not my noreply one. But I had to understand **why** it mattered (privacy on public commits) and make the fix myself.

---

## Day 2 result

- ✅ 2FA, recovery codes, private commit email
- ✅ SSH key with a passphrase, added to GitHub and tested
- ✅ This repo, with `.gitignore`, README and Day 1 guide
- ✅ First commits pushed to GitHub
- ✅ First branch and pull request (this guide!)
