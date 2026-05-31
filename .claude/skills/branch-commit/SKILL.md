---
name: branch-commit
description: Create a new git branch, stage and commit changes, then create a PR to main. Use when the user says "branch and commit", "create branch and commit", or "new branch commit PR".
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(gh *)
---

Create a new branch, commit current changes, and open a PR to main.

## Requirements
- Git repo with a clean or dirty working tree
- GitHub CLI (`gh`) installed and authenticated

## Steps

### 1. Understand current state
Run in parallel:
- `git status`
- `git diff --stat`

### 2. Create and switch to a new branch
Derive a short kebab-case branch name from the staged/unstaged changes (e.g. `feat/add-login`, `fix/null-check`, `chore/update-deps`).

```
git checkout -b <branch-name>
```

### 3. Stage and commit
Stage all changed/new files relevant to the work (avoid `.env`, secrets, binaries):
```
git add <files>
```

Write a concise commit message (imperative mood, under 72 chars):
```
git commit -m "<message>"
```

### 4. Push the branch
```
git push -u origin <branch-name>
```

### 5. Create the PR
Use the `/pr` skill logic: build a structured body and run:
```
gh pr create --title "<title>" --base main --body "<body>"
```

PR body template:
```
## Summary
Brief description of changes

## Changes
- List of specific changes made

## Testing
How to test these changes

## Checklist
- [ ] Tests pass
- [ ] Documentation updated
- [ ] No breaking changes
```

### 6. Report
State the branch name, commit message, and PR URL.

## Notes
- Never commit `.env`, credentials, or binary files.
- Do not delete the branch after PR creation.
- If `gh` auth fails, tell the user to run `! gh auth login`.
