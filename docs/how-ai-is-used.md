# How AI Is Used in This Guide

AI tools such as **Claude** were used while writing this guide, and you'll probably use them while learning too. That's fine. Engineers use AI at work every day.

The important thing is **how** you use it. Think of AI like a **calculator in a maths lesson**: brilliant for speeding things up, but if you can't do the sums yourself, you won't notice when it gives you a wrong answer.

---

## What AI was used for

| Used for | Example |
|---|---|
| **Explaining things simply** | "What does `git push -u origin main` actually do?" |
| **Drafting** | A first version of a guide, a README or a settings file, which is then checked and edited |
| **Reviewing** | Spotting that a Git email was a personal address instead of the private noreply one |
| **Speeding up boring jobs** | Retyping a long file, or clicking through the same GitHub screens again |
| **Checking the official docs** | Looking up the exact format for Claude Code's settings file before using it |

## What AI was NOT used for

| Not used for | Why |
|---|---|
| **Making decisions** | What gets committed, merged or built is a human decision |
| **Touching secrets** | AI never sees passwords, keys, tokens or recovery codes |
| **Changing cloud resources** | Anything that could cost money or break things is done by a person, on purpose |
| **Replacing understanding** | Every command in these guides is explained, so you know what it does |

---

## Why you shouldn't lean on AI

**1. It can be confidently wrong.**
AI can give you a command that looks perfect but is out of date or slightly off. Real-life example: a sat-nav that sends you down a road that's been closed. You still need to look out of the window.

**2. Cloud mistakes cost money or cause security problems.**
One wrong command can delete a server, open a security hole, or leave something running that bills you every hour. The AI doesn't pay that bill. You do.

**3. You have to understand it to check it.**
If you can't explain what a command does, you can't tell whether the AI got it right.

**4. When things break, you're the one fixing them.**
Interviews, on-call problems at 2am, a broken deployment: you need the skills in your own head.

---

## The rules

| Rule | In plain English |
|---|---|
| **Understand before you run** | Know what every part of a command does first |
| **Never paste secrets** | No passwords, AWS keys, private keys or recovery codes, ever |
| **Check the result** | Run `git status`, `ls`, `jq` or `terraform plan` to prove it worked |
| **Official docs win** | AWS, Terraform and GitHub docs are the final word, not the AI |
| **AI suggests, you decide** | Especially for anything that changes or deletes something real |
| **Try it yourself first** | When something is new, have a go before asking. Use AI for the boring repeats. |

---

## How the AI assistant is kept safe in this repo

This repo uses **Claude Code**, an AI assistant that can read files and run commands inside a project. That's powerful, so it has two layers of protection.

Real-life example: you've hired a cleaner.

| Layer | File | Real-life version | How strong |
|---|---|---|---|
| 📝 Instructions | `CLAUDE.md` | A note on the fridge: "Please don't go in the office" | A polite request. Usually followed, but not forced. |
| 🔒 Locks | `.claude/settings.json` | Actually locking the office and the safe | Enforced. Claude Code blocks these actions. |
| 👀 You | Permission prompts | You check what the cleaner wants to do before saying yes | The final check |

**What the locks block:**
- Reading secret folders and files (`~/.aws`, `~/.ssh`, `.env`, `*.pem`, `*.key`, Terraform state files)
- Running `terraform apply` or `terraform destroy`
- Running any `aws` command

**Tested, not assumed:** when Claude Code was asked to read a file in `~/.ssh`, it got back:

```
File is in a directory that is denied by your permission settings.
```

That proves the lock works, not just the note.

**Honest limit:** the locks match the words of a command, so a very unusual command could still slip past. That's why the last layer is **you**: read every permission prompt before clicking **Yes**, and never choose "don't ask again" for anything near your secrets.

---

## In one sentence

> **Use AI like a knowledgeable colleague: ask it questions, let it help with the boring parts, but check its work and keep the important decisions for yourself.**
