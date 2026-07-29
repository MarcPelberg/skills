---
name: reset-agentic-context
description: Perform an explicit, recoverable clean-slate reset of AI coding context for one or more repositories and, only when requested, the current user's Codex and Claude configuration. Use when a user asks to remove or disable inherited AI instructions, memories, prompts, custom skills or agents, hooks, plugins, MCP servers, and related agent-tool configuration while preserving source code, Git history, credentials, sessions, safety policy, and application runtimes. Never invoke implicitly for ordinary cleanup.
---

# Reset Agentic Context

Reset only the scope the user names. Quarantine eligible artifacts instead of deleting
them, journal every change before applying it, and fail closed on ambiguous state.

## Resolve scope

Build both an exact path allowlist and an exact artifact-category allowlist before
writing:

1. Use repository roots named by the user.
2. When the request explicitly asks for user-global configuration only, include no
   repository. When no target type is stated at all, default to the current Git
   repository only.
3. Include other registered worktrees only when the user explicitly includes all
   worktrees or names their paths. Keep application-managed worktrees report-only.
4. Include Codex user-global settings only for explicit wording such as "Codex user
   settings" or "Codex global settings." Include Claude user-global settings only for
   equivalent Claude wording. Include both only when the user explicitly says all
   local AI settings or names both products. The word "local" alone is insufficient.
5. Map requested categories independently: instructions/rules, memories, skills and
   agents, hooks, plugins, MCP/tools, or all agentic context. A category-specific
   request authorizes only that category and the minimum setting change required to
   deactivate it. "Full clean-slate reset" authorizes all listed categories inside
   the path scope.
6. Treat cloud memories, hosted connectors, organization policy, managed settings,
   and extension installation state as report-only.

An explicit request to execute the reset authorizes safe in-scope mutations. Do not
pause for a manifest review when the user says to proceed autonomously. A host-required
filesystem approval may still be requested. If access is denied, skip that scope and
continue.

## Prepare recovery

Choose collision-safe timestamped roots:

- ordinary quarantine: the user's requested location, otherwise
  `$HOME/AI_RESET_QUARANTINE/<timestamp>`;
- protected backups:
  - Windows: `%LOCALAPPDATA%\AgentContextReset\<timestamp>`;
  - macOS: `$HOME/Library/Application Support/AgentContextReset/<timestamp>`;
  - Linux: `${XDG_STATE_HOME:-$HOME/.local/state}/agent-context-reset/<timestamp>`.

Require local, non-reparse destinations. Restrict both roots to the current user and
platform administrators before writing; use owner-only mode on POSIX. Store memories,
histories, mixed-purpose settings, and files that may contain secrets only in protected
backup storage.

Canonicalize both destinations before creating them. Require them to be disjoint from
each other and outside every source, repository, Git administration directory, global
configuration root, protected path, installed skill root, and mutation-allowlisted
tree. If a default overlaps, choose a safe writable user-state directory outside the
allowlist. If no disjoint persistent destination exists, do not mutate.

Before the first mutation, durably create a write-ahead journal. Before each move or
edit, record:

- canonical source and destination;
- artifact type;
- source identity or pre-change content hash;
- intended structural config change; and
- a no-overwrite move rollback or compare-and-swap config rollback action.

Mark an entry complete only after the mutation succeeds. Never record secret values.

## Fixed boundaries

Never change:

- `.git` directories or pointer files, common Git administration, refs, objects,
  indexes, hooks, logs, or branch history;
- source, tests, normal scripts, CI, docs, assets, `.env*`, dependencies, manifests,
  lockfiles, Docker files, or build/runtime configuration;
- nested repositories, submodules, vendored repositories, links, junctions, mount
  points, or other reparse points;
- standalone credentials, tokens, authentication stores, connected accounts,
  sessions, transcripts, checkpoints, task history, rollout files,
  application-managed worktree contents, or any worktree administration;
- model, provider, trust, sandbox, approval, permission, deny, network, safety,
  telemetry, UI, or runtime settings;
- managed or organization policy;
- application, extension, or plugin binaries, caches, databases, or global storage;
- system-owned skills such as `.system`;
- this `reset-agentic-context` skill and its package ancestors; or
- enabled recurring automations and their minimum proven dependencies.

Do not fetch, pull, checkout, merge, rebase, stash, commit, push, create branches,
change refs, rewrite history, uninstall software, or empty quarantine.

If an eligible AI configuration embeds a credential, preserve its exact original bytes
only in protected backup storage. Never print or transform the credential.

## Path and ownership safety

For every candidate:

1. Compare normalized canonical paths using platform-appropriate case rules.
2. Check every source and destination path component without following links.
3. Enforce component-boundary containment inside the exact allowlist.
4. Require current-user ownership for user-global mutation.
5. Recheck identity, containment, ownership, and reparse state immediately before
   changing anything.
6. Skip unknown, shared, managed, outside-home, or ownership-ambiguous state unless
   the user named that exact path.

Resolve the executing `SKILL.md` by file identity. Protect it, every installed copy of
this skill, each package directory, and their ancestors from parent-level moves.

## Reset repositories

For every allowlisted repository or worktree:

1. Resolve the absolute root and `git rev-parse --git-common-dir`.
2. Record branch or detached HEAD and
   `git status --porcelain=v2 --branch --untracked-files=all`.
3. Skip mutation when merge, rebase, cherry-pick, revert, bisect, `git am`, sequencer
   state, an index lock, or a locked-worktree marker is active.
4. Inventory exact candidates without traversing `.git`, dependency folders,
   generated build output, nested repositories, quarantine, or links.
5. Treat matched directories as classification roots. Move a parent atomically only
   when every descendant is eligible and none is protected, unknown, managed,
   mixed-purpose, nested-repository, or self state.
6. Immediately before each mutation, recheck Git operation state and compare the
   recorded HEAD, index, and status fingerprint.
7. Journal and move eligible leaves without overwriting. Surgically edit shared
   settings under the config transaction rules below.
8. Leave every noncandidate dirty change untouched.

### Repository candidates

Match these exact filenames at any applicable instruction depth:

- `AGENTS.md`
- `AGENTS.override.md`
- `CLAUDE.md`
- `CLAUDE.local.md`
- `GEMINI.md`
- `SKILLS.md`
- `.cursorrules`
- `.windsurfrules`
- `.clinerules`
- `.roomodes`
- `.mcp.json`
- `.copilot-instructions.md`
- `.github/copilot-instructions.md`
- `.vscode/mcp.json`

Inspect these exact directories without moving them wholesale:

- `.agents`
- `.skills`
- `.claude`
- `.codex`
- `.cursor`
- `.windsurf`
- `.cline`
- `.gemini`
- `.mcp`
- `.prompt`
- `.prompts`
- `.context`
- `.continue`
- `.roo`
- `.github/agents`
- `.github/instructions`
- `.github/prompts`

Match only these Aider configuration files:

- `.aider.conf.yml`
- `.aiderignore`
- `.aider.conventions.md`
- `.aider.model.settings.yml`
- `.aider.model.metadata.json`

Keep other `.aider*` history, input, cache, or unknown files report-only.

For `.claude/settings.json`, `.claude/settings.local.json`, and
`.codex/config.toml`, edit only the allowlisted keys in the product sections below.
Preserve all other keys.

## Retain only proven automation dependencies

When the user requests a clean slate, retain an agent artifact only when all are true:

1. an enabled recurring scheduler, CI workflow, or standard package task directly
   references it;
2. removing it would break that recurring task;
3. an ordinary script, package command, CI job, Make target, or documentation cannot
   replace it; and
4. it is the minimum required dependency.

Do not retain a component merely because it may be useful for manual work. Preserve an
ordinary script referenced by an agent wrapper; quarantine only the wrapper.

## Edit shared configuration transactionally

For JSON, JSONC, YAML, or TOML containing both eligible and protected state:

1. Hash the original and journal the backup plan.
2. Copy its exact bytes, ACLs/mode, and supported metadata to protected storage.
3. Parse with a format-aware parser.
4. Change only an exact allowlisted key below.
5. Compare the structural diff with the allowed-key set.
6. Recheck the original hash.
7. Atomically replace the file in its directory while preserving permissions and
   supported metadata.
8. Parse the result, confirm protected keys are structurally unchanged, and journal
   the post-edit hash.
9. On immediate failure, restore only while the live file still matches the failed
   transaction's expected hash.

For later rollback, replace an edited config with its backup only when the live file
still matches the journaled post-edit hash. If it changed afterward, preserve the
live file and backup separately and report that manual reconciliation is required.
No-overwrite rollback applies to moved artifacts; compare-and-swap rollback applies
to edited configs.

Skip instead of guessing when the format, schema, ownership, diff, or transaction
guarantee is ambiguous.

## Reset Codex user context

Run this section only when user-global Codex cleanup is in scope.

Resolve `CODEX_HOME`; default to `$HOME/.codex`. Mutate an alternate root only when it
is under the current user's home or the user named its exact path, it is locally
owned, it contains no linked path component, and Codex user-global cleanup is in
scope. Inspect the resolved and default roots separately if they differ.

Reset:

- `$CODEX_HOME/AGENTS.md` and `AGENTS.override.md`;
- user-authored agents and prompts under `$CODEX_HOME/agents`,
  `$CODEX_HOME/prompts`, and `$CODEX_HOME/instructions`;
- nonretained skill packages under `$HOME/.agents/skills` and, when used by the local
  client, `$CODEX_HOME/skills`;
- `$CODEX_HOME/memories` and
  `$CODEX_HOME/memories_extensions/chronicle`, using protected storage; and
- recognized active-context settings in `$CODEX_HOME/config.toml`,
  `$CODEX_HOME/*.config.toml`, and in-scope project `.codex/config.toml`.

Allow only these Codex config changes:

- classify each `mcp_servers.<name>` entry; preserve retained entries and remove only
  nonretained custom entries;
- remove `hooks`;
- remove `model_instructions_file` and `project_doc_fallback_filenames`;
- remove nonretained custom entries under `agents` and `skills.config`;
- set `features.memories = false`;
- set `memories.generate_memories = false`;
- set `memories.use_memories = false`; and
- set an existing nonretained plugin-scoped MCP `enabled` field to `false`.

Preserve `.system`, this skill, retained dependencies, command safety policy, auth,
sessions, history, state, logs, worktrees, plugin packages, and caches. Report other
installed plugin or connector exposure instead of deleting it.

## Reset Claude user context

Run this section only when user-global Claude cleanup is in scope.

Resolve `CLAUDE_CONFIG_DIR`; default to `$HOME/.claude`. Mutate an alternate root only
when it is under the current user's home or the user named its exact path, it is
locally owned, it contains no linked path component, and Claude user-global cleanup
is in scope. Inspect the resolved and default roots separately if they differ.

Reset:

- global and in-scope project `CLAUDE.md`, `CLAUDE.local.md`,
  `.claude/CLAUDE.md`, and individual `.claude/rules` files;
- nonretained user-authored packages under `skills`, `commands`, `output-styles`,
  `agents`, `workflows`, and `agent-memory`;
- auto memory mapped by canonical metadata to in-scope repositories under
  `<CLAUDE_CONFIG_DIR>/projects/<project>/memory`; and
- recognized active-context settings in user and project settings files.

For every in-scope repository, inspect its ancestor chain for exact `CLAUDE.md`,
`CLAUDE.local.md`, `.claude/CLAUDE.md`, and individual `.claude/rules` files. Mutate
an ancestor only when user-global Claude cleanup is explicitly in scope, the ancestor
is inside the current user's home, and all ownership and path checks pass. Keep shared,
filesystem-root, and outside-home ancestors report-only unless the user named the
exact path.

Allow only these `settings.json` or `settings.local.json` changes:

- remove `hooks`;
- set nonretained `enabledPlugins` entries to `false`;
- remove corresponding nonretained `pluginConfigs` and
  `extraKnownMarketplaces` entries;
- remove `outputStyle`;
- set `autoMemoryEnabled` to `false`;
- remove `autoMemoryDirectory` only after safely classifying its target; and
- remove only `env.CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD`.

For `$HOME/.claude.json`, edit only:

- nonretained entries under root `mcpServers`; and
- within project records matched to in-scope canonical repositories, nonretained
  `mcpServers` entries and their names in `enabledMcpjsonServers` or
  `disabledMcpjsonServers`.

For an in-scope project `.mcp.json`, parse a direct server map or root `mcpServers`
map and classify servers individually. Move the whole file to protected storage only
when every entry is nonretained and the root contains no unknown keys. Otherwise
remove only nonretained entries transactionally.

When the user includes Claude Desktop configuration, inspect its platform-standard
`claude_desktop_config.json` and classify each root `mcpServers.<name>` entry. Preserve
retained servers and remove only nonretained custom entries.

Preserve `.credentials.json`, identity and machine IDs, OAuth, permissions, deny
rules, sessions, transcripts, checkpoints, file history, project history, UI state,
plugin packages, extension storage, and caches.

Treat all managed settings, managed MCP policy, and operating-system policy stores as
report-only.

## Inspect launch-time loaders

When user-global cleanup is in scope:

- preserve `CODEX_HOME` and `CLAUDE_CONFIG_DIR`; use them to locate real roots;
- inspect user and machine environment variable names without printing values;
- remove only user-scope
  `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD`;
- keep machine-scope variables and shell profiles report-only;
- in VS Code or Cursor settings, remove only
  `claudeCode.environmentVariables.CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD`;
- preserve all other IDE settings; and
- report installed AI extensions and externally hosted tools without uninstalling
  them or deleting global storage.

## Verify

After mutation:

1. Rescan every in-scope repository and worktree.
2. Recheck Git status and diff; confirm every new change maps to a journaled candidate.
3. Confirm no protected path or key changed.
4. Rescan user-global roots and list remaining active or external context.
5. Validate every edited config and automatically restore invalid edits.
6. Confirm memory use is inactive where editable.
7. Finalize the journal, skipped-target list, remaining-surface list, and exact
   no-overwrite rollback commands.
8. Report results per repository and per product, plus the quarantine roots.
9. State that already-running tasks retain loaded context; require fresh tasks or an
   application restart before evaluating the reset.

Never claim remote branches are current without fetching. Never claim cloud,
managed, or inaccessible state was reset.
