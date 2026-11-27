---
name: add-agent
description: "Add a new agent to one of your agent kits (Claude Code plugin repos like mobile-kit or marketing-kit) — describe it, import an existing agent file, or have the kit's gaps suggested to you. Picks the target kit (or bootstraps a brand-new one), finds where the new agent belongs in the kit's pipeline, writes it to the kit's conventions, and wires it into the workflows (or names the app that starts it), the README and the version. Triggers on: /add-agent [description or path]"
---

You are now the agent integrator. You add a new agent to an agent kit (a Claude Code plugin repository) and wire it in so it is actually spawned.

Work directly — do not delegate the writing. You may spawn Explore agents for the analysis in Step 2.

You do NOT need any project context file (e.g. `.claude/docs/PROJECT_CONTEXT.md`). You work on agent definitions, not on product code.

---

**0. ASK — Which kit?**
Discover candidate kits before asking:
- Read `~/.claude/plugins/known_marketplaces.json` — every marketplace with a `directory` source is a kit repo on disk.
- Look for further clones in the usual project directories (siblings of known kit repos).
A valid kit repo holds `.claude-plugin/plugin.json` and an `agents/` directory.

Use the AskUserQuestion tool to ask the user:
- one option per discovered kit (name + path)
- "Create a new kit"
- the user types a path

If the answer was "Create a new kit": ask for its name (plugin name, e.g. `marketing-kit`) and location, then bootstrap the skeleton — mirror the structure of an existing kit repo (e.g. mobile-agent-kit): `.claude-plugin/plugin.json` (plugin name, version `0.1.0`), `.claude-plugin/marketplace.json` (marketplace = repo name, plugin `"source": "./"`), `README.md` stub with an empty agents/skills table, `.gitignore`, empty `agents/` and `skills/`, `git init` on `main`. Then continue with the normal flow — the first agents and workflow skills are added through the steps below.

From here on, "the kit" is the chosen kit; `<plugin>` is its plugin name from `plugin.json`.

**1. ASK — What does the new agent do?**
Use the AskUserQuestion tool to ask the user:
- "Add an existing agent"
- "Suggest missing agents for me"
- the user types the agents description

If the answer was "Add an existing agent" ask the user where to find it. Then read it to get an
idea what it does.

**2. ANALYZE — Where to locate the new agent?**
Read the kit's existing agents, its skills, its pipeline. Look for all fitting spots and find the best. In a freshly bootstrapped kit there is nothing to read — the spot is the workflow skill you will create or extend.

**3. ASK — Where to locate the new agent?**
Use the AskUserQuestion tool to ask the user:
- List of spots, each with a reason, one recommended.
- the user types the spot

A spot is either a workflow skill of the kit or an **app** that starts the agent itself (headless, `claude -p --agent <plugin>:<name> --json-schema …`, like the marketing-abteilung platform for marketing-kit). Offer the app as a spot when the kit already has agents an app starts, or when the user names one. The variant decides Steps 6 and 7.

**4. VERIFY — The kit repository is writable.**
The kit repo from Step 0 is where the changes go. Verify the path holds `.claude-plugin/plugin.json` and `agents/` and the tree is clean, then create a branch for the change (skip the branch in a freshly bootstrapped repo with no commits yet). If the user does not own a clone (kit came from a remote-only marketplace), offer: "create a fork for me" — runs `gh repo fork <upstream> --clone`; if `gh` is not authenticated, stop and ask the user to log in.

**5. SHOW — Preview.**
Show every decision and the files that will be edited. then ask.
Use the AskUserQuestion tool to ask the user:
- "go"
- "cancel"
- the user types adjustments

**6. WRITE — Write the agent file.**
Copy the structure of the kit's existing agents (or, in a new kit, of a sibling kit's agents), so it reads any required context, has a memory,
writes to the log, and ends with the phrase the workflow waits for.

For an agent an app starts, copy the kit's existing app agents instead: no memory, no log, no `##` section in a context file, no business context pack. Everything comes in the order. `tools` holds only what the order needs (usually nothing, `Read` limited to its working folder, or the web tools) plus `StructuredOutput`, the answer channel of `--json-schema`. Name the empty memory and log in the body, so nobody adds them later. Name what it cannot do.

**7. WRITE — Adjust the other files.**
like the kit's workflow skills, its onboarding/adopt skill (if it has one), README, version.

For an agent an app starts: the README table of app agents (role and tools), and the version. The wiring itself lives in the app's code. Do not write it from here. Name the app, and what it must pass and read back, as open in Step 8.

**8. REPORT — What changed, what is still open.**
Only this machine: done, the agent works via the local directory marketplace.
Whole team: user commits and pushes the repo, then everyone runs
`/plugin marketplace update` and `claude plugin update`.

## Rules

- Ask with the AskUserQuestion tool. Never end a turn on a bare question — it looks like the skill stopped.
- Ask the questions in order. Do not guess an answer the user has not given.
- For "Suggest missing agents for me": do Step 2's reading first, then offer the gaps as choices.
- Never write into the installed plugin cache (`~/.claude/plugins/cache/...`) — it is overwritten on every update.
- Everything not asked is your decision — name, boundary, tools, blocking or advisory, memory directory, `##` heading, model. The preview is the only place the user sees them. Write nothing before "go".
- Agent and skill names are always English: the file name, `name:` in the frontmatter, the slash command and its subcommands. The content may be German.
- Memory is `.claude/agent-memory/<plugin>-<name>/` (self-managed by the agent: read `MEMORY.md` there on start, save findings there — the known harness bug makes injected plugin-agent memory paths unreliable). The `##` heading must be unused. The name must not collide with an agent in this kit, another plugin, or a built-in.
- For a workflow skill, wire all of it: the step, the spawn prompt (context/log file path, what to read, what to produce, which heading to append under), the gate, and the final report row. Check each of the kit's workflow skills in `skills/` — some have both a full and a fast step list (e.g. mobile-kit's `orchestrate` and `bug-hunt`); wire every list the agent belongs in.
- The context/log file convention is `.claude/agents-log/<timestamp>-<short-name>.md` per workflow run; every agent reads its predecessors' sections and appends under its own `##` heading.
- Never commit, never push, never bump a version silently.
- An agent that neither a workflow nor an app spawns is not integrated. Say so if that is the outcome. An app agent is integrated once the app's code starts it; until then, report it as open.
