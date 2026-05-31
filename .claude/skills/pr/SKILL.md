---
name: pr
description: Create a well-documented pull request from the current branch to main, review it on GitHub, post inline comments if issues found, and merge if clean. Use when the user says "make PR", "create PR", "PR to main", or "create and merge PR".
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(gh *)
---

> Based on the [Create PR skill](https://www.claudedirectory.org/skills/pr) from Claude Directory.

Create well-documented pull requests, review them on GitHub, post inline comments if issues found, and merge when clean.

## Requirements
- GitHub CLI (`gh`) installed and authenticated
- On a feature branch (not main)

## Steps

### 1. Understand the branch state
Run in parallel:
- `git status`
- `git log main..HEAD --oneline`
- `gh repo view --json owner,name` (get owner/repo for API calls)

### 2. Create the PR
Generate a descriptive PR title (under 70 chars) and build the body:

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

Note the PR number from the output.

### 3. Fetch the live PR diff from GitHub
```
gh pr diff <pr_number>
gh pr view <pr_number> --json headRefOid
```

Use `gh pr diff` (not local `git diff`) so the review is against what GitHub sees.

### 4. Review the GitHub diff
Analyse the diff for:
- Correctness bugs
- Security issues
- Missing error handling
- Code style or logic concerns

**If issues are found:**
Get the latest commit SHA: `gh pr view <pr_number> --json headRefOid -q .headRefOid`

Post each finding as an inline PR comment on GitHub:
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

### 5. Merge (only if review is clean)
```
gh pr merge <pr_number> --merge --subject "<title>" --body "Reviewed on GitHub and merged by Claude — no issues found."
```

Do **not** delete the branch after merging.

### 6. Report
State the PR URL, review outcome (issues found or clean), and whether it was merged.

## Notes
- If `gh` returns an auth error, tell the user to run `! gh auth login`.
- Always use `--merge` (not squash/rebase) unless the user specifies otherwise.
- Include Screenshots section only if there are UI-related changes.
- Never delete the branch after merging.
