---
description: Generate a project follow-up report for non-tech stakeholders based on recent PRs.
---

## Goal

Generate a concise, non-technical project follow-up file (`follow_up_YYYYMMDD.md`) summarizing recent progress based on GitHub Pull Requests. The report is written in English and targets non-technical stakeholders.

## Step 1 — Gather parameters

Use AskUserQuestion to ask the user the following **three questions in a single call**:

1. **Start date**: "From which date should PRs be included? (YYYY-MM-DD format)"
   - Options: "Last week", "Last 2 weeks", "Last month", "Other (specify YYYY-MM-DD)"

2. **PR filter**: "Which PRs should be included?"
   - Options: "Merged only (Recommended)", "All (open + merged + closed)"

3. **GitHub user filter**: "Should the report cover a specific GitHub user or all contributors?"
   - Options: "All contributors (Recommended)", "A specific user"

If the user selects "A specific user", ask a follow-up question to get the GitHub username.

## Step 2 — Fetch PRs

Use the `gh` CLI to list PRs matching the parameters:

```
# Merged only (default)
gh pr list --state merged --search "merged:>=<START_DATE>" --json number,title,body,mergedAt,author,labels --limit 100

# All PRs
gh pr list --state all --search "created:>=<START_DATE>" --json number,title,body,state,createdAt,mergedAt,closedAt,author,labels --limit 100
```

If a specific GitHub user was selected, add `--author <username>` to the command.

## Step 3 — Analyze and categorize

For each PR, read the title and body. Categorize changes into **business-friendly themes** such as:

- **New Features** — user-visible functionality added
- **Improvements** — enhancements to existing features (performance, UX, reliability)
- **Bug Fixes** — issues resolved
- **Infrastructure & DevOps** — deployment, CI/CD, monitoring (keep brief, non-technical)
- **Other** — anything that doesn't fit above

Skip purely internal/technical PRs that have zero stakeholder relevance (e.g. dependency bumps, linting fixes). If unsure, include them briefly under "Infrastructure".

## Step 4 — Write the follow-up file

Create the file at the project root: `follow_up_YYYYMMDD.md` (where YYYYMMDD is today's date).

Use this structure:

```markdown
# Project Follow-Up — <Month Day, Year>

**Period covered:** <start_date> to <today>
**PRs included:** <merged only | all> — <N> PRs total
**Contributors:** <all | specific user>

---

## Highlights

<2-4 sentence executive summary of the most impactful changes>

## New Features

- **<Feature name>** — <1-2 sentence plain-English description> (PR #<number>)

## Improvements

- **<Improvement>** — <1-2 sentence description> (PR #<number>)

## Bug Fixes

- **<Fix>** — <1-2 sentence description> (PR #<number>)

## Infrastructure & DevOps

- <Brief item> (PR #<number>)

---

_Generated on <today's date>._
```

**Writing guidelines:**

- Use plain, non-technical language. Avoid code terms, file names, or library names.
- Focus on **what changed for the user/product**, not how it was implemented.
- Group related PRs into a single bullet when they contribute to the same feature.
- Omit empty sections (e.g. if there are no bug fixes, skip that section).
- Keep the total report concise — ideally 1 page.

## Step 5 — Present the result

Tell the user the file has been created and show a brief summary of what's in it.
