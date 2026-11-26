# Day 3: PATH, duplicates and aliases

## Goal
Fix a duplicate Homebrew entry in PATH and add time-saving aliases.

## 1. Spot the problem
```
which -a brew
```
Shows every copy of `brew` the shell can find. Two lines means PATH has a duplicate.

## 2. Find the cause
- `/etc/paths.d/homebrew` added Homebrew to PATH.
- `brew shellenv` in `~/.zprofile` added it again.

## 3. Fix it
1. Back up first: `cp /etc/paths.d/homebrew ~/homebrew-paths.backup`
2. Remove the extra file: `sudo rm /etc/paths.d/homebrew`
3. Stop future duplicates: add `typeset -U path` to the end of `~/.zprofile`
4. Check: `which -a brew` now shows one line.

## 4. Add aliases
An alias is a nickname for a longer command.
```
echo "alias gs='git status'" >> ~/.zshrc
echo "alias ga='git add'" >> ~/.zshrc
echo "alias gl='git log --oneline'" >> ~/.zshrc
source ~/.zshrc
```
Use `>>` (adds to the end), never `>` (overwrites the file).

## What I learnt
- Always back up a file before changing it.
- `source` reloads settings without restarting Terminal.