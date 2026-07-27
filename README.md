# skills

Self-contained [Agent Skills](https://agentskills.io) that work in both **Claude Code** and **OpenAI Codex**. Each skill is a single `SKILL.md` — no dependencies, no setup, no references to anything outside its own folder. Drop a folder in, and it works.

| Skill | What it does |
| --- | --- |
| [`show-dont-tell`](show-dont-tell/SKILL.md) | Plans, specs, and approvals ship as **mermaid diagrams first** — never walls of text. A picture is worth a thousand words: the diagram leads, a ~5-line prose chaser follows. Voice-neutral, so it composes with any persona skill. |
| [`mean-dad-nice-mom`](mean-dad-nice-mom/SKILL.md) | Judges work like a **mean dad** — evidence over effort, half-done is 0% shipped, never reports green on a skipped check — and delivers the verdict like a **nice mom**: warm, specific, zero flattery, zero cruelty. Anti-sycophancy and anti-abrasiveness in one contract. Pairs well with `show-dont-tell`. |

## Install

One clone covers both runtimes — run whichever install lines you need:

```bash
git clone https://github.com/MarcPelberg/skills.git

# Claude Code
mkdir -p ~/.claude/skills && cp -r skills/show-dont-tell skills/mean-dad-nice-mom ~/.claude/skills/

# OpenAI Codex
mkdir -p ~/.codex/skills && cp -r skills/show-dont-tell skills/mean-dad-nice-mom ~/.codex/skills/
```

### No clone — fetch straight in

```bash
# Claude Code
for s in show-dont-tell mean-dad-nice-mom; do
  mkdir -p ~/.claude/skills/$s
  curl -fsSL https://raw.githubusercontent.com/MarcPelberg/skills/main/$s/SKILL.md -o ~/.claude/skills/$s/SKILL.md
done

# OpenAI Codex
for s in show-dont-tell mean-dad-nice-mom; do
  mkdir -p ~/.codex/skills/$s
  curl -fsSL https://raw.githubusercontent.com/MarcPelberg/skills/main/$s/SKILL.md -o ~/.codex/skills/$s/SKILL.md
done
```

### Copy-paste by hand

Each skill is one plain-text file. Open the raw link, copy everything, and save it as `~/.claude/skills/<name>/SKILL.md` (Claude Code) or `~/.codex/skills/<name>/SKILL.md` (Codex):

- [`show-dont-tell` raw text](https://raw.githubusercontent.com/MarcPelberg/skills/main/show-dont-tell/SKILL.md)
- [`mean-dad-nice-mom` raw text](https://raw.githubusercontent.com/MarcPelberg/skills/main/mean-dad-nice-mom/SKILL.md)

That's it. The skills trigger automatically when their descriptions match what you're doing — presenting a plan, reviewing a spec, reporting status — or invoke them by name.

## Why these exist

- **`show-dont-tell`**: a diagram forces a plan to commit to order, branches, and failure paths that prose lets it mumble through — and you can veto a box faster than you can veto a paragraph.
- **`mean-dad-nice-mom`**: "You're absolutely right!" is lying kindly; "this is garbage" is truth that stopped caring who hears it. This skill bans both for the same reason.

## License

MIT — see [LICENSE](LICENSE).
