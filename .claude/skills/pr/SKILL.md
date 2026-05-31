---
name: pr
description: Create a well-documented pull request from the current branch to main, review it, post inline comments if issues found, and merge if clean. Use when the user says "make PR", "create PR", "PR to main", or "create and merge PR".
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(gh *)
---

> Based on the [Create PR skill](https://www.claudedirectory.org/skills/pr) from Claude Directory.

Create well-documented pull requests with comprehensive descriptions, review them, and merge when clean.

## Requirements
- GitHub CLI (`gh`) installed and authenticated
- On a feature branch (not main)

## Steps

### 1. Understand the branch state
Run these in parallel:
- `git status`
- `git log main..HEAD --oneline`
- `git diff main...HEAD`

### 2. Create the PR
Generate a descriptive PR title (under 70 chars) and build the body using this template:

```
## Summary
Brief description of changes

## Changes
- List of specific changes made

## Testing
How to test these changes

## Screenshots
(if applicable)

## Checklist
- [ ] Tests pass
- [ ] Documentation updated
- [ ] No breaking changes
```

Run: `gh pr create --title "<title>" --base main --body "<body>"`

### 3. Review the PR
Analyse the full diff for:
- Correctness bugs
- Security issues
- Missing error handling
- Code style or logic concerns

**If issues are found:** Post each finding as an inline PR comment using the GitHub API:
```
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments \
  --method POST \
  --field body="<comment>" \
  --field commit_id="<sha>" \
  --field path="<file>" \
  --field line=<line_number>
```
Then report a summary of findings to the user and **do not merge** — let the user fix and re-invoke `/pr` or `/merge`.

**If no issues found:** Proceed to merge.

### 4. Merge (only if review is clean)
```
gh pr merge <number> --merge --subject "<title>" --body "Reviewed and merged by Claude — no issues found."
```

Do **not** delete the branch after merging.

### 5. Report
State the PR URL, review outcome, and whether it was merged.

## Notes
- If `gh` returns an auth error, tell the user to run `! gh auth login`.
- Always use `--merge` (not squash/rebase) unless the user specifies otherwise.
- Include Screenshots section only if there are UI-related changes.
- Never delete the branch after merging.
