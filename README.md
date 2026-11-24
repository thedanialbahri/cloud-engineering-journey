# Cloud Engineering: A Hands-On Guide from Zero

A step-by-step, beginner-friendly guide to the core skills of a **junior Cloud Engineer**: the terminal, Git, **AWS**, **Terraform** and **automation** with Bash, Python and GitHub Actions.

Every guide is written so you can **follow along on your own machine**, with plain-English explanations of what each command does and why it matters.

---

## Who this is for

- Beginners moving into cloud from IT support, helpdesk or another career
- Anyone who wants to learn by **doing**, not just watching videos
- People who want to understand **why** a command works, not just copy it

No prior cloud experience is needed.

## How to use this repo

1. Start with **Day 1** and work through the guides in order. Each one builds on the last.
2. Type the commands yourself rather than copying and pasting. It sticks better.
3. Read the output after every command. The answer (or the error) is usually right there.
4. Try the **challenges** before opening the answers.

## Guides

| # | Guide | What you'll learn |
|---|---|---|
| 1 | [Terminal, Permissions, Homebrew and jq](docs/day-01-terminal-homebrew-jq.md) | Terminal basics, reading file permissions, installing tools, working with JSON |
| 2 | [Git, GitHub and SSH](docs/day-02-git-github-ssh.md) | Commits, branches, pull requests, securing GitHub, SSH keys, `.gitignore` |

More guides are added as the programme progresses.

## Roadmap

| Stage | Focus |
|---|---|
| 1 | Linux, networking, Git, cloud fundamentals |
| 2 | AWS compute, storage, networking, IAM and security |
| 3 | Terraform: modules, state, repeatable deployments |
| 4 | Python, Bash, APIs, GitHub Actions and CI/CD |
| 5 | Docker, monitoring, logging and resilience |
| 6 | End-to-end projects that bring it all together |

## Principles used throughout

| Principle | In practice |
|---|---|
| **Security first** | No secrets in Git, SSH keys with passphrases, 2FA everywhere, least-privilege access |
| **Cost aware** | Budgets and alerts before creating cloud resources, and everything torn down after use |
| **Verify, don't assume** | Every change is checked, for example with `git status`, `ls` or `terraform plan` |
| **Understand before running** | Every command is explained before you use it |
| **AI as a tool, not a crutch** | AI can help explain and draft, but you check its work and make the decisions |

## Setup used in these guides

- macOS on Apple Silicon (most steps also work on Intel Macs and Linux)
- zsh, Homebrew, Git, AWS CLI v2, Terraform, jq, VS Code

## Cost

Early guides cost **£0**. Guides that create AWS resources say so clearly at the start, explain the likely cost, and show you how to clean up afterwards.

---

## About the author

Written by **Danial Bahri**, a UK-based IT professional moving from first-line support into cloud engineering. Each guide is based on hands-on work, including the real problems hit along the way and how they were fixed.

Open to junior Cloud Engineer, Cloud Support and Infrastructure roles (UK or remote).
