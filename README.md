# Cloud Engineering: A Hands-On Guide from Zero

A friendly, step-by-step guide to the skills a **junior Cloud Engineer** uses every day: the terminal, Git, **AWS**, **Terraform** and **automation**.

Every guide is written so you can follow along on your own computer. Each command is explained in plain English, with real-life examples, so you understand **why** it works and aren't just copying it.

---

## Who this is for

- Anyone starting out in cloud, whether from IT support, helpdesk or a completely different job
- People who learn best by **doing**
- People who want to understand what a command does before they run it

You don't need any cloud experience to start.

## How to use this guide

1. Start with **Day 1** and go in order. Each day builds on the one before.
2. Type the commands yourself. It sticks better than copying and pasting.
3. Read what the terminal says after each command. The answer (or the error) is usually right there.
4. Have a go at the **challenges** before you open the answers.
5. Stuck on a word or command? Look it up in the [Commands and Jargon Reference](docs/reference.md).

## Guides

| Day | Guide | What you'll learn |
|---|---|---|
| 1 | [Terminal, Permissions, Homebrew and jq](docs/day-01-terminal-homebrew-jq.md) | Moving around the terminal, reading file permissions, installing tools, working with JSON |
| 2 | [Git, GitHub, SSH and a Safe AI Assistant](docs/day-02-git-github-ssh.md) | Commits, branches, pull requests, securing GitHub, SSH keys, `.gitignore`, setting up Claude Code safely |

More guides are added as the course goes on.

## Extra pages

| Page | What's in it |
|---|---|
| [Commands and Jargon Reference](docs/reference.md) | Every command and term used so far, explained simply with examples |
| [How AI Is Used in This Guide](docs/how-ai-is-used.md) | How AI helps with learning, where it doesn't, and the safety rules around it |

## What's coming

| Stage | Focus |
|---|---|
| 1 | Linux, networking, Git and cloud basics |
| 2 | AWS: servers, storage, networking, users and security |
| 3 | Terraform: building cloud setups from code |
| 4 | Automation: Python, Bash, APIs and GitHub Actions |
| 5 | Docker, monitoring, logging and keeping things running |
| 6 | Bigger projects that bring it all together |

## The habits this guide teaches

| Habit | What it looks like |
|---|---|
| **Security first** | Secrets never go in Git. SSH keys have passphrases. 2FA is on everywhere. |
| **Watch the cost** | Budget alerts go on before you build anything in the cloud, and everything gets cleaned up afterwards. |
| **Check, don't assume** | After every change, check it worked (`git status`, `ls`, `terraform plan`). |
| **Understand before you run** | If you can't say what a command does, don't run it yet. |
| **AI is a helper, not a crutch** | AI can explain and draft, but you check its work and make the decisions. |

## What you'll need

- A Mac (most steps also work on Linux)
- About 10–15 hours a week
- A free GitHub account
- An AWS account (from Stage 2). Early guides cost **£0**. Any guide that costs money says so at the start and shows you how to clean up.

## Repo layout

```
.
├── README.md            ← you are here
├── CLAUDE.md            ← rules for the AI assistant in this repo
├── .claude/
│   └── settings.json    ← locks that stop the AI touching secrets
├── .gitignore           ← files Git must never save
└── docs/
    ├── day-01-...md     ← daily guides
    ├── day-02-...md
    ├── reference.md     ← commands and jargon
    └── how-ai-is-used.md
```
