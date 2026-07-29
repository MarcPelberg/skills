# Marc's skills

Five useful add-ons for AI coding assistants. Three are Marc-made skills that work in both **Claude Code** and **OpenAI Codex**; two are excellent third-party plugins maintained upstream. Each Marc-made skill's portable core is a single plain-text file the AI reads and follows.

This repository contains skills written and maintained by Marc. It also recommends a couple of larger third-party skill packages below, with links that install them directly from their maintainers.

## What they do, in plain English

A skill is a sticky note for your AI: "when this situation comes up, handle it this way." The AI notices your request matches a note and follows it.

The first three live in this repository. Superpowers and Ponytail remain upstream so their maintainers can keep them current.

[`show-dont-tell`](show-dont-tell/SKILL.md) — **Marc-made.** When you ask "how should we build this?", you normally get eight paragraphs to wade through. With this skill you get a flowchart — boxes, arrows, where things branch, what happens on failure — plus a couple of sentences under it. You can veto a box in five seconds; vetoing a paragraph takes three minutes.

[`mean-dad-nice-mom`](mean-dad-nice-mom/SKILL.md) — **Marc-made.** AI assistants have two bad habits: flattering you ("Looks great, ship it!" without really checking), and — when you demand brutal honesty — being just plain brutal. This skill splits the job in two. The **mean dad** does the judging: nothing is believed without proof, "80% done" counts as not done, no rubber stamps. The **nice mom** does the talking: what's broken, said specifically and kindly, plus what's genuinely good. So instead of *"Looks great!"* you get *"Tests pass, but this line can double-charge a customer — here's the fix, and you're one change from shipping."* Strict verdict, kind delivery.

[`reset-agentic-context`](reset-agentic-context/SKILL.md) — **Marc-made.** Safely starts an AI-assisted repository over without pretending that deleting every hidden folder is harmless. It inventories active instructions, memories, skills, hooks, plugins, and MCP servers; moves eligible items into recoverable quarantine; journals rollback before each change; and protects source code, Git, credentials, sessions, safety policy, and application runtimes. It can reset one repository or, only when explicitly requested, the current user's Codex and Claude settings.

[Superpowers](https://github.com/obra/superpowers) — **Third-party.** A structured development workflow for coding agents: brainstorming, planning, test-driven development, debugging, code review, and verification. Install it from its official marketplace so it stays current.

[Ponytail](https://github.com/DietrichGebert/ponytail) — **Third-party.** A minimal-code discipline for coding agents: reuse what exists, prefer the platform and standard library, and write only what the task needs without skipping safety checks.

The five are independent — use whichever combination fits your work.

## When do they run?

Three modes:

- **Automatic (default)** — `show-dont-tell` and `mean-dad-nice-mom` run when the AI sees a matching request. Quick factual questions won't wake them, by design. `reset-agentic-context` never runs implicitly.
- **On call** — name it: *"use show-dont-tell"*, *"mean-dad-nice-mom this review"*, or, in Codex, *"use $reset-agentic-context in this repository only"*. Fires every time.
- **Always on** — both tools read a personal instructions file at the start of every session. This mode is intended for the first two skills, not the reset skill. Add a line for whichever one you want:

Superpowers and Ponytail use their own plugin activation and hook rules after installation; their official setup is below.

  **Claude Code** — add to `~/.claude/CLAUDE.md` (create the file if it doesn't exist):

  ```
  Always apply the show-dont-tell skill to every response.
  ```

  ```
  Always apply the mean-dad-nice-mom skill to every response.
  ```

  **OpenAI Codex** — add the same line(s) to `~/.codex/AGENTS.md`.

  Delete a line to put that skill back on automatic. Never add the reset skill here.

## Install Marc's skills

Pick whichever way suits you. Installing means getting the skill folder into your assistant's skills folder — `~/.claude/skills/` for Claude Code, `~/.codex/skills/` for Codex. To install just one skill, drop the other names from the commands.

### With git

**Bash (macOS, Linux, Git Bash, or WSL)**

```bash
git clone https://github.com/MarcPelberg/skills.git

# Claude Code
mkdir -p ~/.claude/skills && cp -r skills/show-dont-tell skills/mean-dad-nice-mom skills/reset-agentic-context ~/.claude/skills/

# OpenAI Codex
mkdir -p ~/.codex/skills && cp -r skills/show-dont-tell skills/mean-dad-nice-mom skills/reset-agentic-context ~/.codex/skills/
```

**PowerShell (Windows)**

```powershell
git clone https://github.com/MarcPelberg/skills.git

$source = Join-Path (Get-Location) 'skills'
$names = @('show-dont-tell', 'mean-dad-nice-mom', 'reset-agentic-context')
$destinations = @(
  (Join-Path $HOME '.claude\skills'),
  (Join-Path $HOME '.codex\skills')
)

foreach ($destination in $destinations) {
  New-Item -ItemType Directory -Force -Path $destination | Out-Null
  foreach ($name in $names) {
    $skillSource = Join-Path $source $name
    $skillDestination = Join-Path $destination $name
    New-Item -ItemType Directory -Force -Path $skillDestination | Out-Null
    Get-ChildItem -LiteralPath $skillSource -Force |
      Copy-Item -Destination $skillDestination -Recurse -Force
  }
}
```

### Without cloning — fetch straight in with Bash

```bash
# Claude Code
for s in show-dont-tell mean-dad-nice-mom reset-agentic-context; do
  mkdir -p ~/.claude/skills/$s
  curl -fsSL https://raw.githubusercontent.com/MarcPelberg/skills/main/$s/SKILL.md -o ~/.claude/skills/$s/SKILL.md
done

# OpenAI Codex
for s in show-dont-tell mean-dad-nice-mom reset-agentic-context; do
  mkdir -p ~/.codex/skills/$s
  curl -fsSL https://raw.githubusercontent.com/MarcPelberg/skills/main/$s/SKILL.md -o ~/.codex/skills/$s/SKILL.md
done

# Required for Codex explicit-only reset invocation
mkdir -p ~/.codex/skills/reset-agentic-context/agents
curl -fsSL https://raw.githubusercontent.com/MarcPelberg/skills/main/reset-agentic-context/agents/openai.yaml -o ~/.codex/skills/reset-agentic-context/agents/openai.yaml
```

### No terminal — copy and paste by hand

Each skill's portable core is one plain-text file. Open the link, copy everything, and save it as `~/.claude/skills/<skill-name>/SKILL.md` (Claude Code) or `~/.codex/skills/<skill-name>/SKILL.md` (Codex):

- [`show-dont-tell` raw text](https://raw.githubusercontent.com/MarcPelberg/skills/main/show-dont-tell/SKILL.md)
- [`mean-dad-nice-mom` raw text](https://raw.githubusercontent.com/MarcPelberg/skills/main/mean-dad-nice-mom/SKILL.md)
- [`reset-agentic-context` raw text](https://raw.githubusercontent.com/MarcPelberg/skills/main/reset-agentic-context/SKILL.md)

For `reset-agentic-context` in Codex, also save
[`openai.yaml`](https://raw.githubusercontent.com/MarcPelberg/skills/main/reset-agentic-context/agents/openai.yaml)
as `~/.codex/skills/reset-agentic-context/agents/openai.yaml`. That metadata prevents
implicit invocation.

## Recommended third-party packages

These are excellent companions, but their code does **not** live in this repository. Both are complete, actively maintained plugin projects with their own skills, hooks, releases, and documentation. Installing from upstream keeps them current and makes it clear who maintains the code.

### Third-party · [Superpowers](https://github.com/obra/superpowers)

A structured software-development workflow for coding agents: brainstorming, planning, test-driven development, debugging, code review, and verification.

**Claude Code**

```text
/plugin install superpowers@claude-plugins-official
```

**OpenAI Codex**

- **Codex app:** open **Plugins** in the sidebar, find **Superpowers** under **Coding**, and click **+**.
- **Codex CLI:** enter `/plugins`, search for `superpowers`, and choose **Install Plugin**.

[See all official Superpowers installation options →](https://github.com/obra/superpowers#installation)

### Third-party · [Ponytail](https://github.com/DietrichGebert/ponytail)

A minimal-code discipline for coding agents: reuse what exists, prefer the platform and standard library, and write only what the task needs without cutting safety checks.

**Claude Code** — send these as two separate prompts:

```text
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail
```

**OpenAI Codex**

```bash
codex plugin marketplace add DietrichGebert/ponytail
codex plugin add ponytail@ponytail
```

Then open `/hooks`, review and trust Ponytail's two lifecycle hooks, and start a new task. Restart the Codex app if you use the desktop app. Ponytail's automatic activation requires Node.js on your `PATH`; its skills still work without the hooks.

[See all official Ponytail installation options →](https://github.com/DietrichGebert/ponytail#install)

## License

MIT — see [LICENSE](LICENSE). Free to use, copy, and modify.
