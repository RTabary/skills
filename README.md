# Skills & Prompts Collection

A personal library of reusable AI skills and prompts, stored as `.prompt.md` files. Each skill is a self-contained instruction set that can be loaded into LLMs and AI coding assistants to automate recurring workflows.

## Skill format

Each skill is a Markdown file with YAML frontmatter:

```markdown
---
description: Short description of what the skill does.
---

## Goal

What the skill accomplishes.

## Step 1 — ...

Detailed instructions the AI will follow.
```

The `description` field is used by tools to display the skill in selection menus.

## Setup by tool

### Claude Code (Anthropic CLI)

Claude Code natively supports `.prompt.md` files as slash commands.

1. Clone this repo (or symlink it) so the files are accessible from your project:

   ```sh
   # Option A — clone alongside your project
   git clone <repo-url> ~/skills

   # Option B — symlink into your project
   ln -s ~/skills/.prompt-files my-project/.claude/commands/
   ```

2. In your project, skills placed under `.claude/commands/` become available as `/command-name`. For example, `follow-up.prompt.md` becomes `/follow-up`.
3. Skills placed under `~/.claude/commands/` are available globally across all projects.

> See the [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code/slash-commands) for more details on custom slash commands.

### GitHub Copilot (VS Code / JetBrains)

Copilot supports reusable prompts via the `.github/prompts/` directory.

1. Copy or symlink skill files into your project:
   ```sh
   mkdir -p my-project/.github/prompts
   cp ~/skills/*.prompt.md my-project/.github/prompts/
   ```
2. In VS Code, open Copilot Chat and type `#` followed by the prompt file name to attach it to your conversation.
3. You can also reference prompts in `copilot-instructions.md` for automatic inclusion.

> See the [GitHub Copilot docs](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions) for more details.

### Cursor

Cursor uses a `.cursor/rules/` directory for custom instructions.

1. Copy or symlink skill files:
   ```sh
   mkdir -p my-project/.cursor/rules
   cp ~/skills/*.prompt.md my-project/.cursor/rules/
   ```
2. Rules are automatically loaded based on file glob patterns or can be invoked manually via `@rules` in the chat.

> See the [Cursor docs](https://docs.cursor.com/context/rules) for more details.

### Windsurf

Windsurf supports custom rules via a `.windsurfrules` file or a `rules/` directory.

1. Copy skill files into your project:
   ```sh
   mkdir -p my-project/.windsurf/rules
   cp ~/skills/*.prompt.md my-project/.windsurf/rules/
   ```
2. Rules are picked up automatically by the AI assistant.

### ChatGPT / Custom GPTs

For ChatGPT or API-based usage, paste the content of a `.prompt.md` file directly into:

- A **Custom GPT** system prompt
- A **system message** in the API
- The conversation as a user message prefixed with "Follow these instructions:"

### Generic (any LLM)

Any `.prompt.md` file works as a standalone prompt. Copy-paste its content into any LLM chat interface or API call. The structured Markdown format is understood by all major models.

## License

Personal use. Feel free to fork and adapt.
