# skills

Self-contained [Agent Skills](https://agentskills.io) that work in both **Claude Code** and **OpenAI Codex**. Each skill is a single `SKILL.md` — no dependencies, no setup, no references to anything outside its own folder. Drop a folder in, and it works.

| Skill | What it does |
| --- | --- |
| [`show-dont-tell`](show-dont-tell/SKILL.md) | Plans, specs, and approvals ship as **mermaid diagrams first** — never walls of text. A picture is worth a thousand words: the diagram leads, a ~5-line prose chaser follows. |
| [`mean-dad-nice-mom`](mean-dad-nice-mom/SKILL.md) | Judges work like a **mean dad** — evidence over effort, half-done is 0% shipped, never reports green on a skipped check — and delivers the verdict like a **nice mom**: warm, specific, zero flattery, zero cruelty. Anti-sycophancy and anti-abrasiveness in one contract. |

## Install

The skills are fully independent — install either one, or both. One clone covers both runtimes; drop whichever folder name you don't want from the `cp` lines:

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

## When do they run?

Three modes:

- **Automatic (default)** — the AI sees your request matches a skill's description (presenting a plan, asking for a review, "sanity check this") and applies it on its own. Quick factual questions won't wake them, by design.
- **On call** — name it: *"use show-dont-tell"* or *"mean-dad-nice-mom this review"*. Fires every time.
- **Always on** — both tools read a personal instructions file at the start of every session. One line there makes a skill apply to every response. Add a line for whichever skill you want:

  **Claude Code** — add to `~/.claude/CLAUDE.md` (create the file if it doesn't exist):

  ```
  Always apply the show-dont-tell skill to every response.
  ```

  ```
  Always apply the mean-dad-nice-mom skill to every response.
  ```

  **OpenAI Codex** — add the same line(s) to `~/.codex/AGENTS.md`.

  Delete a line to put that skill back on automatic.

## What these do, in plain English

A skill is just a text file of instructions your AI reads before answering — a sticky note that says "when this situation comes up, handle it this way." The AI sees your request matches a note, and follows it. That's the whole mechanism.

**`show-dont-tell`** — when you ask "how should we build this?", you normally get eight paragraphs to wade through. With this skill you get a flowchart — boxes, arrows, where things branch, what happens on failure — plus a couple of sentences under it. A diagram forces the plan to commit to an order and its failure paths, and you can veto a box in five seconds; vetoing a paragraph takes three minutes.

**`mean-dad-nice-mom`** — AI assistants have two bad habits: flattering you ("Looks great, ship it!" without really checking), and — when you demand brutal honesty — being just plain brutal. This skill splits the job in two. The **mean dad** does the judging: nothing is believed without proof, "80% done" counts as not done, no rubber stamps. The **nice mom** does the talking: what's broken, said specifically and kindly, plus what's genuinely good. So instead of *"Looks great!"* you get *"Tests pass, but this line can double-charge a customer — here's the fix, and you're one change from shipping."* Strict verdict, kind delivery.

## License

MIT — see [LICENSE](LICENSE).
