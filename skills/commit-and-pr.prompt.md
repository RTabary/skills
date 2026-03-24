---
description: Create a conventional commit message with description, and optionally create a PR with a professional description.
---

## Goal

Help the user create a well-structured git commit following the [Conventional Commits](https://www.conventionalcommits.org/) specification, then optionally create a GitHub Pull Request with a professional, detailed description.

## Step 1 — Analyze changes

Run the following commands to understand the current state of the working tree:

```bash
git status
git diff --staged
git diff
git log --oneline -5
```

If there are no staged changes, inform the user and ask if they want to stage specific files or all changes before proceeding.

### Security check — sensitive files

Before proceeding, scan the staged files **and** untracked files for sensitive content that should never be committed. Flag any file matching these patterns:

- `.env`, `.env.*` (environment variables, secrets)
- `*.pem`, `*.key`, `*.p12`, `*.pfx` (private keys, certificates)
- `credentials.json`, `secrets.json`, `service-account*.json`
- `*.secret`, `*.credential`
- `id_rsa`, `id_ed25519` or any SSH private key
- `token`, `*.token`
- Any file containing API keys, passwords, or tokens in its content

If any suspicious file is found in the staged changes:
1. **Stop immediately** — do not proceed to commit
2. List the flagged files with a clear warning
3. Recommend the user unstage them (`git reset HEAD <file>`) and add them to `.gitignore`
4. Ask the user to confirm before continuing

Even if no file matches the patterns above, always prefer staging files **by name** rather than using `git add .` or `git add -A`, to reduce the risk of accidentally including sensitive files.

## Step 2 — Determine commit type and scope

Based on the staged changes, determine the most appropriate conventional commit **type**:

| Type         | When to use                                              |
|--------------|----------------------------------------------------------|
| `feat`       | A new feature                                            |
| `fix`        | A bug fix                                                |
| `docs`       | Documentation only changes                               |
| `style`      | Formatting, missing semicolons, etc. (no code change)    |
| `refactor`   | Code change that neither fixes a bug nor adds a feature  |
| `perf`       | Performance improvement                                  |
| `test`       | Adding or correcting tests                               |
| `build`      | Changes to build system or external dependencies         |
| `ci`         | Changes to CI configuration files and scripts            |
| `chore`      | Other changes that don't modify src or test files        |
| `revert`     | Reverts a previous commit                                |

Also identify a **scope** (the area of the codebase affected, e.g. `auth`, `api`, `ui`). The scope is optional — only include it if it adds clarity.

Check if the change includes a **breaking change**. If so, add `!` after the type/scope (e.g. `feat(api)!:`) and include a `BREAKING CHANGE:` footer in the body.

## Step 3 — Propose the commit message

Present the commit message to the user using this format:

```
<type>(<scope>): <short imperative description (max 72 chars)>

<body: explain WHAT changed and WHY, not HOW — wrap at 72 chars>
```

**Rules for the subject line:**
- Use imperative mood ("add", "fix", "update", not "added", "fixed", "updated")
- Do not capitalize the first letter after the colon
- No period at the end
- Maximum 72 characters total

**Rules for the body:**
- Separate from subject with a blank line
- Explain the motivation for the change and contrast with previous behavior
- Wrap lines at 72 characters
- Use bullet points if multiple changes are involved

Use AskUserQuestion to ask the user:

1. **"Does this commit message look good?"**
   - Options: "Yes, commit", "Edit the message", "Cancel"

If the user chooses "Edit the message", ask them what they want to change and propose an updated message.

## Step 4 — Create the commit

Once the user approves the message, create the commit using a HEREDOC:

```bash
git commit -m "$(cat <<'EOF'
<approved commit message>
EOF
)"
```

Confirm the commit was created successfully by running `git status`.

## Step 5 — Propose PR creation

Use AskUserQuestion to ask:

1. **"Would you like to create a Pull Request?"**
   - Options: "Yes, create a PR", "No, just the commit is fine"

If the user declines, stop here and confirm the commit is done.

## Step 6 — Gather PR context

If the user wants a PR, run:

```bash
git log --oneline main..HEAD
git diff main...HEAD --stat
```

Use AskUserQuestion to ask the following **two questions in a single call**:

1. **"What is the target branch for this PR?"**
   - Options: "main (Recommended)", "develop", "Other"

2. **"What type of change is this?"**
   - Options: "Feature", "Bug fix", "Refactor", "Documentation", "Other"

## Step 7 — Create the PR

Based on the full diff (`git diff main...HEAD`), the commit history, and the user's answers, create the PR using `gh`:

```bash
gh pr create --title "<concise PR title>" --body "$(cat <<'EOF'
## Summary

<2-4 sentences explaining the purpose and context of this PR>

## Changes

- <bullet list of key changes, grouped logically>

## Motivation

<Why this change is needed — business or technical context>

## Testing

- [ ] <checklist of testing steps or verification done>

## Notes

<Any additional context, screenshots links, migration steps, or follow-up items — omit this section if not needed>
EOF
)"
```

**PR title rules:**
- Short, under 72 characters
- Use imperative mood
- Prefix with conventional commit type if helpful (e.g. "feat: add user authentication")

**PR description guidelines:**
- Be professional and clear
- Focus on the "what" and "why", not implementation details
- Include testing steps so reviewers know how to verify
- Omit empty sections
- Keep it concise but complete

If the current branch doesn't have an upstream, push it first:

```bash
git push -u origin HEAD
```

## Step 8 — Present the result

Show the user the PR URL and a brief summary of what was created.
